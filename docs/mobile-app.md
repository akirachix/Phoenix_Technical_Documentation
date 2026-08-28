


# Mobile App

The mobile app (Flutter) is used by Cooperative Agents and Wholesale Buyers for field operations, campaign management, and finance tracking.

![Pamodzi mobile home dashboard](assets/images/mobile-screens/screen-home-dashboard.png)

## Screens

<div class="grid cards" markdown>

-   :material-login:{ .lg .middle } **Login**

    ---

    Buyer login/signup plus a separate agent login path (`agent-login.dart`, `buyer-login.dart`, `buyer-signup.dart`).

-   :material-view-dashboard:{ .lg .middle } **Home Dashboard**

    ---

    Campaign KPIs, aggregation progress, activity feed (`homescreen.dart`).

-   :material-rocket-launch:{ .lg .middle } **Demand Campaign**

    ---

    Buyer launches a funded campaign — crop, quantity, grade, moisture, province (`demand_campaign_form/demand_campaign.dart`).

-   :material-account-circle:{ .lg .middle } **Profile**

    ---

    Account settings, theme switching (`profile_screen.dart`).

-   :material-clipboard-list:{ .lg .middle } **Campaign List**

    ---

    Active/aggregating/fulfilled campaigns at a glance (`campaign_list/campaign_list.dart`).

-   :material-check-decagram:{ .lg .middle } **Produce Verification**

    ---

    Agent flow for approving/rejecting a farmer's listing (`ProduceVerificationBody`).

-   :material-account-plus:{ .lg .middle } **Farmer Registration**

    ---

    Agent onboards a farmer on their behalf (`FarmerRegistrationBody`).

-   :material-chart-line:{ .lg .middle } **Market Price**

    ---

    Price trend charts by crop (`MarketPriceScreen`).

</div>

## Screens in Detail

=== "Login"

    ![Login](assets/images/mobile-screens/screen-login.png)

    Buyer login with email/password. A separate "Login as Cooperative Agent" button routes to `staff_id`-based auth instead.

=== "Home Dashboard"

    ![Home Dashboard](assets/images/mobile-screens/screen-home-dashboard.png)

    Demand campaign value, mass aggregated, cooperative hub count, daily/weekly volume, aggregation-progress ring, and a recent-activity feed by province.

=== "Demand Campaign"

    ![Demand Campaign](assets/images/mobile-screens/screen-demand-campaign.png)

    Crop, quantity, grade, moisture, and province — maps directly to the `demandcampaign` table in [Database](database.md).

=== "Profile"

    ![Profile](assets/images/mobile-screens/screen-profile-logout.png)

    Confirm-before-logout modal, consistent with the token-blacklist logout flow in [Security](security.md).

!!! info "Remaining screens"
    Campaign List, Produce Verification, Farmer Registration, and Market Price don't have exports yet — add them here the same way once available.

## Architecture Pattern

Navigation routing, screen state lifecycle, and view component structure are centered on `MainScreen` and `PamodziBottomNav`.

<div class="grid cards" markdown>

-   :material-account-arrow-right:{ .lg .middle } **Onboarding & Authentication**

    ---

    Onboarding carousel (`PamodziOnboardingFlow`), agent login (`AgentLoginScreen`), buyer login/signup (`buyer-login.dart`, `buyer-signup.dart`).

-   :material-tractor:{ .lg .middle } **Operations**

    ---

    Farmer registration (`FarmerRegistrationBody`), produce quality verification (`ProduceVerificationBody`).

-   :material-storefront:{ .lg .middle } **Marketplace & Finance**

    ---

    Active demand campaigns (`CampaignListBody`), market price charts (`MarketPriceScreen`), financial statements (`FinancialDetailsScreen`).

-   :material-cog:{ .lg .middle } **Settings**

    ---

    Dynamic light/dark theme switching, user profile management (`ProfileScreen`).

</div>

!!! tip "Offline Behavior"
    Local data persistence with delayed synchronization, built for low-connectivity rural regions.

## Project Structure

??? note "pamodzi/ — full Flutter project tree"

```text
    pamodzi/
    ├── .github/
    ├── android/
    ├── assets/
    ├── build/
    ├── fonts/
    ├── ios/
    ├── linux/
    ├── macos/
    ├── test/
    ├── web/
    ├── windows/
    ├── lib/
    │   ├── models/
    │   │   ├── campaign.dart
    │   │   ├── dashboard_model.dart
    │   │   ├── market_price_model.dart
    │   │   ├── notification_item.dart
    │   │   └── profile_model.dart
    │   ├── screens/
    │   │   ├── campaign_details/
    │   │   │   └── campaign_details.dart
    │   │   ├── campaign_list/
    │   │   │   └── campaign_list.dart
    │   │   ├── demand_campaign_form/
    │   │   │   └── demand_campaign.dart
    │   │   ├── main/
    │   │   │   └── main_screen.dart
    │   │   ├── notifications/
    │   │   ├── agent-login.dart
    │   │   ├── buyer-login.dart
    │   │   ├── buyer-signup.dart
    │   │   ├── homescreen.dart
    │   │   ├── market_price_screen.dart
    │   │   ├── profile_screen.dart
    │   │   └── splash_screen.dart
    │   ├── services/
    │   │   └── api_service.dart
    │   ├── widgets/
    │   │   ├── bottom_nav.dart
    │   │   ├── pamodzi_header.dart
    │   │   └── status_badge.dart
    │   └── main.dart
    ├── .gitignore
    ├── analysis_options.yaml
    ├── pubspec.yaml
    ├── pubspec.lock
    └── README.md
```