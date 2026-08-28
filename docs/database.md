# Database

## Entity Overview

| Table | Purpose |
|---|---|
| `user` | Root identity record for every person on the platform; role-based |
| `farmer` | Farmer-specific profile, linked to a user and a cooperative |
| `cooperative` | A cooperative hub — location, province, contact info |
| `cooperative_agent` | Staff account tied to a cooperative, manages field operations |
| `wholesale_buyer` | Buyer company profile, linked to a user |
| `produce_listing` | A single farmer's produce entry, pending aggregation |
| `produce_pool` | Aggregated produce assigned against a demand campaign |
| `demandcampaign` | A buyer's request for crop/grade/quantity, with funding status |
| `payment_transactions` | Ledger of all money movement (buyer charges, payouts) |
| `merchant_accounts` | Platform's Flutterwave merchant wallet(s) and balances |
| `sms_notification` | Log of every SMS/in-app notification sent, with delivery status |
| `marketprice` | Reference commodity prices by province and date |
| `ussd` | Raw USSD session data before it becomes a formal produce listing |
| `token_blacklist` | Revoked JWTs (logout/invalidation) |

## Data Dictionary

### `user`

| Column | Type | Constraints |
|---|---|---|
| `user_id` | UUID | PK, server-generated |
| `first_name` | String(100) | NOT NULL |
| `last_name` | String(100) | NOT NULL |
| `phone_number` | String(20) | NOT NULL, UNIQUE |
| `nrc_number` | String(20) | NOT NULL, UNIQUE |
| `created_at` | DateTime (tz) | NOT NULL, default now |
| `is_archived` | Boolean | default False |
| `role` | Enum: `admin`, `cooperative_agent`, `farmer`, `wholesale_buyer` | NOT NULL |
| `email` | String(100) | UNIQUE, nullable |
| `password_hash` | String(255) | nullable |

**Constraints:** Non-admin users must **not** have `password_hash`/`email` set; admins **must** have `password_hash` set (enforced via a DB `CheckConstraint`).
**Relationships:** 1–1 with `wholesale_buyer`, `farmer`, `cooperative_agent`.

### `farmer`

| Column | Type | Constraints |
|---|---|---|
| `farmer_id` | UUID | PK, server-generated |
| `user_id` | UUID | FK → `user.user_id`, NOT NULL |
| `cooperative_id` | UUID | FK → `cooperative.cooperative_id`, NOT NULL |
| `pin_hash` | String(255) | NOT NULL |
| `is_verified` | Boolean | default False |

### `cooperative`

| Column | Type | Constraints |
|---|---|---|
| `cooperative_id` | UUID | PK, server-generated |
| `cooperative_name` | String(50) | NOT NULL, UNIQUE |
| `address` | String(150) | NOT NULL |
| `phone_number` | String(20) | NOT NULL |
| `created_at` | DateTime (tz) | NOT NULL, default now |
| `is_active` | Boolean | default True |
| `province` | Enum (10 Zambian provinces) | NOT NULL |
| `latitude` | Numeric(8,6) | NOT NULL |
| `longitude` | Numeric(9,6) | NOT NULL |

> Provinces: Central, Copperbelt, Eastern, Luapula, Lusaka, Muchinga, Northern, North-Western, Southern, Western.

### `cooperative_agent`

| Column | Type | Constraints |
|---|---|---|
| `agent_id` | UUID | PK, server-generated |
| `user_id` | UUID | FK → `user.user_id`, NOT NULL |
| `cooperative_id` | UUID | FK → `cooperative.cooperative_id`, NOT NULL |
| `staff_id` | String(20) | NOT NULL, UNIQUE |
| `password_hash` | String(255) | NOT NULL |
| `agent_status` | Enum `AgentStatusEnum` | default `ACTIVE`, NOT NULL |

### `wholesale_buyer`

| Column | Type | Constraints |
|---|---|---|
| `buyer_id` | UUID | PK, server-generated |
| `user_id` | UUID | FK → `user.user_id`, NOT NULL |
| `password_hash` | String(255) | NOT NULL |
| `email` | String(50) | NOT NULL, UNIQUE |
| `company_name` | String(50) | NOT NULL |
| `pacra_number` | String(50) | NOT NULL (Zambian company registration number) |
| `is_archived` | Boolean | default False |

### `produce_listing`

| Column | Type | Constraints |
|---|---|---|
| `produce_id` | UUID | PK, server-generated |
| `farmer_id` | UUID | FK → `farmer.farmer_id`, NOT NULL |
| `cooperative_id` | UUID | FK → `cooperative.cooperative_id`, NOT NULL |
| `produce_pool_id` | UUID | FK → `produce_pool.produce_pool_id`, nullable |
| `ussd_id` | UUID | FK → `ussd.ussd_id`, nullable |
| `produce_type` | String(50) | NOT NULL |
| `produce_status` | Enum: `not aggregated`, `aggregated`, `archived`, `rejected`, `approved` | NOT NULL, default `not aggregated` |
| `produce_quantity` | Integer | NOT NULL, CHECK > 0 |
| `listed_at` | DateTime (tz) | NOT NULL, default now |

### `produce_pool`

| Column | Type | Constraints |
|---|---|---|
| `produce_pool_id` | UUID | PK, server-generated |
| `campaign_id` | UUID | FK → `demandcampaign.campaign_id`, NOT NULL |
| `created_at` | DateTime (tz) | NOT NULL, default now |
| `allocated_weight` | Float | NOT NULL |

### `demandcampaign`

| Column | Type | Constraints |
|---|---|---|
| `campaign_id` | UUID | PK, server-generated |
| `target_cooperative_id` | UUID | FK → `cooperative.cooperative_id`, NOT NULL |
| `buyer_id` | UUID | FK → `wholesale_buyer.buyer_id`, NOT NULL |
| `campaign_status` | Enum: `aggregating`, `fulfilled`, `cancelled`, `archived` | nullable, default None |
| `crop_type` | Enum: `maize`, `sorghum`, `groundnuts`, `millet` | NOT NULL |
| `grade` | Enum: `A`, `B`, `C` | NOT NULL |
| `province` | Enum (10 Zambian provinces) | NOT NULL |
| `quantity` | Numeric(10,2) | NOT NULL, CHECK > 0 |
| `amount` | Numeric(12,2) | NOT NULL |
| `payment_confirmed` | Boolean | default False |
| `start_date` | DateTime (tz) | NOT NULL |
| `end_date` | DateTime (tz) | NOT NULL, CHECK `end_date > start_date` |
| `is_extended` | Boolean | default False |
| `extension_reminder_sent` | Boolean | default False |
| `moisture_content` | Numeric(4,2) | NOT NULL |

### `payment_transactions`

| Column | Type | Constraints |
|---|---|---|
| `transaction_id` | UUID | PK, client-generated (`uuid4`) |
| `campaign_id` | UUID | FK → `demandcampaign.campaign_id`, NOT NULL |
| `buyer_id` | UUID | FK → `wholesale_buyer.buyer_id`, NOT NULL |
| `cooperative_id` | UUID | FK → `cooperative.cooperative_id`, nullable |
| `merchant_wallet_id` | UUID | FK → `merchant_accounts.merchant_wallet_id`, nullable |
| `amount` | Numeric(10,2) | NOT NULL |
| `transaction_fee` | Numeric(10,2) | default 0.00 |
| `platform_fee` | Numeric(10,2) | default 0.00 |
| `net_payout_amount` | Numeric(10,2) | default 0.00 |
| `currency` | String(10) | default `"ZMW"` |
| `status` | String(50) | default enum |
| `type` | String(50) | NOT NULL |
| `mobile_money_provider` | String(20) | nullable |
| `flutterwave_ref` | String(255) | nullable |
| `platform_internal_ref` | String(255) | NOT NULL, UNIQUE |
| `payout_destination_meta` | JSON | nullable |
| `metadata_json` | JSON | nullable |
| `created_at` | DateTime (tz) | default now |
| `updated_at` | DateTime (tz) | default now, on update now |

### `merchant_accounts`

| Column | Type | Constraints |
|---|---|---|
| `merchant_wallet_id` | UUID | PK |
| `flutterwave_merchant_id` | String | UNIQUE, nullable |
| `initial_balance` | Numeric(10,2) | default 0.00 |
| `current_balance` | Numeric(10,2) | default 0.00 |
| `created_at` | DateTime (tz) | default now |
| `is_active` | Enum `MerchantStatus` | default — *(confirm default value)* |

### `sms_notification`

| Column | Type | Constraints |
|---|---|---|
| `notification_id` | UUID | PK, server-generated |
| `recipient_phone` | String(15) | nullable, CHECK length ≥ 9 |
| `notification_type` | Enum: `listing_confirmation`, `dropoff_reminder`, `campaign_extension_request`, `campaign_cancelled_refund`, `campaign_cancellation_blocked`, `buyer_produce_pickup` | NOT NULL |
| `channel` | Enum: `sms`, `in_app` | default `sms` |
| `message` | Text | NOT NULL |
| `status` | Enum: `pending`, `sent`, `failed`, `read` | default `pending` |
| `response` | Enum: `pending`, `accepted`, `rejected` | nullable |
| `responded_at` | DateTime (tz) | nullable |
| `reference_id` | UUID | nullable |
| `error_detail` | Text | nullable |
| `sent_at` | DateTime (tz) | nullable |
| `created_at` | DateTime (tz) | default now |
| `farmer_id` | UUID | FK → `farmer.farmer_id`, nullable |
| `buyer_id` | UUID | FK → `wholesale_buyer.buyer_id`, nullable |

### `marketprice`

| Column | Type | Constraints |
|---|---|---|
| `commodity` | String | PK (composite) |
| `province` | String | PK (composite) |
| `price_date` | Date | PK (composite) |
| `price` | Numeric(10,2) | NOT NULL |
| `unit` | String | NOT NULL |
| `source_last_modified` | String | nullable |

### `ussd`

| Column | Type | Constraints |
|---|---|---|
| `ussd_id` | UUID | PK, client-generated (`uuid4`) |
| `phone_number` | String(20) | NOT NULL |
| `crop_type` | String(50) | NOT NULL |
| `quantity` | Float | NOT NULL |
| `status` | String(30) | default `"not_aggregated"` |
| `created_at` | DateTime | default now |

### `token_blacklist`

| Column | Type | Constraints |
|---|---|---|
| `token` | String(1024) | PK |
| `blacklisted_at` | DateTime | default now (UTC) |

## Relationships (ERD Summary)

| From | Cardinality | To |
|---|---|---|
| Cooperative | One-to-many | Cooperative Agent |
| Buyer | One-to-many | Demand Campaign |
| Farmer | One-to-many | Produce Listing |
| Demand Campaign | One-to-many | Produce Listing |
| Demand Campaign | One-to-one | Payment |
| Cooperative | One-to-one | Location |
| Farmer | One-to-one | Cooperative |
| Produce Pool | One-to-many | Produce Listing |
| Merchant Account | One-to-many | Buyer |
| Cooperative | One-to-many | Produce Listing |
| Wholesale Buyer | One-to-many | Payment Transactions |
| Demand Campaign | One-to-one | Produce Pool |
| User | One-to-one | Farmer |
| User | One-to-one | Wholesale Buyer |
| User | One-to-one | Cooperative Agent |

