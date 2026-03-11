# 🌍 Planetze

**Planetze** is a native Android application that helps users track, understand, and reduce their personal carbon footprint. By logging everyday activities across transportation, food, shopping, and energy, users receive real-time emission feedback, interactive visual dashboards, and actionable eco-friendly habit suggestions.

---

## 📋 Table of Contents

- [Features](#-features)
- [Screenshots](#-screenshots)
- [Technology Stack](#-technology-stack)
- [Architecture](#-architecture)
- [Prerequisites](#-prerequisites)
- [Getting Started](#-getting-started)
- [Firebase Setup](#-firebase-setup)
- [Project Structure](#-project-structure)
- [User Flow](#-user-flow)
- [Emission Calculations](#-emission-calculations)
- [Eco Habits](#-eco-habits)
- [Testing](#-testing)
- [Contributing](#-contributing)

---

## ✨ Features

### 🔐 Authentication
- User registration with email & password
- Email verification before first login
- Secure login via Firebase Authentication
- Password reset ("Forgot Password") via email

### 📝 Onboarding Questionnaire
- First-time users complete a comprehensive annual emissions survey
- Covers four categories: **Transportation**, **Food**, **Housing**, and **Consumption**
- Calculates a personalised annual CO₂ baseline
- Compares results against country average and global average
- Results include per-category breakdown and global percentile

### 📊 Eco Gauge (Dashboard)
- Interactive **Pie Chart** showing emission breakdown by category
- **Line Chart** showing emission trends over time
- Switch between **Annual**, **Monthly**, **Weekly**, and **Daily** views
- Side-by-side comparison with country and global averages

### 🚗 Eco Tracker (Daily Logging)
Log daily activities across three modules:

| Module | What you can log |
|--------|-----------------|
| **Transportation** | Personal vehicle (Gasoline/Diesel/Hybrid/Electric), Bus, Train, Subway, Short-haul flight, Long-haul flight, Cycling, Walking |
| **Food** | Beef, Pork, Chicken, Fish, Plant-based meals (number of servings) |
| **Shopping & Energy** | Electronics (Smartphone, Laptop, Tablet, TV), Clothing, Miscellaneous purchases, Gas/Electricity/Water bills |

- Pick any past or current date via a date picker
- See cumulative emissions for the selected day in real time
- View and delete previously logged activities

### 🌱 Eco Habits
- Browse 18 curated eco-friendly habits across four categories (Transportation, Energy, Food, Consumption)
- Each habit is tagged with an impact level: **High**, **Medium**, or **Low**
- Mark habits as active and track your progress
- Habit engagement is reflected in overall emission reduction estimates

### 🔄 Recalculate
- Re-run the onboarding questionnaire at any time to update your annual baseline

---

## 📸 Screenshots

> Screenshots will be added once the app is built and running on a device/emulator.

---

## 🛠 Technology Stack

| Layer | Technology |
|-------|-----------|
| **Language** | Java (primary), XML (layouts/resources) |
| **Min SDK** | API 26 (Android 8.0 Oreo) |
| **Target/Compile SDK** | API 34 (Android 14) |
| **Build System** | Gradle 8.7.2 with Kotlin DSL (`.kts`) |
| **Backend** | Firebase Authentication 23.1.0, Firebase Realtime Database 21.0.0 |
| **UI** | AndroidX AppCompat 1.7.0, Material Design Components 1.12.0, ConstraintLayout 2.2.0 |
| **Charts** | MPAndroidChart 3.1.0 |
| **Data Processing** | Apache POI 5.2.3 (reading `.xlsx` emission data files) |
| **Country Picker** | Country Code Picker (CCP) 2.7.1 |
| **Unit Testing** | JUnit 4.13.2, Mockito 5.5.0 |
| **UI Testing** | Espresso 3.6.1 |

---

## 🏗 Architecture

Planetze follows the **MVP (Model-View-Presenter)** pattern for authentication flows, and a standard **Activity-based** pattern for the rest of the application.

```
┌─────────────────────────────────────────┐
│                   View                  │
│  (Activity / Fragment / XML Layouts)    │
└──────────────────┬──────────────────────┘
                   │  delegates to
┌──────────────────▼──────────────────────┐
│               Presenter                 │
│  (LoginActivityPresenter)               │
└──────────────────┬──────────────────────┘
                   │  calls
┌──────────────────▼──────────────────────┐
│                 Model                   │
│  (LoginActivityModel, FirebaseManager,  │
│   AnnualCalculator, QuestionsRepo)      │
└──────────────────┬──────────────────────┘
                   │  reads/writes
┌──────────────────▼──────────────────────┐
│            Firebase Backend             │
│  (Authentication + Realtime Database)   │
└─────────────────────────────────────────┘
```

### Key Design Decisions
- **Polymorphic Activity Elements**: `EmissionActivityElement` (abstract base) is extended by `TransportationActivityElement`, `FoodConsumptionActivityElement`, and `ShoppingActivityElement`. Each overrides `getEmissions()` for category-specific calculations.
- **Centralised Firebase Access**: `FirebaseManager.java` wraps all Realtime Database reads and writes, providing typed helpers for user data and activity storage.
- **Data-driven Questions**: Onboarding questions and emission formulas are loaded from `QuestionsRepo` and bundled `.xlsx` files, making it easy to update without code changes.

### Firebase Realtime Database Schema

```
users/
└── {uid}/
    ├── email          (String)
    ├── firstName      (String)
    ├── lastName       (String)
    ├── firstLogin     (boolean)
    ├── AnnualAnswers/ (Map – calculated annual totals per category)
    └── activities/
        └── {yyyy-MM-dd}/
            ├── Transportation/
            │   └── {activityId}/ → TransportationActivityElement fields
            ├── FoodConsumption/
            │   └── {activityId}/ → FoodConsumptionActivityElement fields
            └── Shopping/
                └── {activityId}/ → ShoppingActivityElement fields
```

---

## 📦 Prerequisites

- **Android Studio** Hedgehog (2023.1.1) or later
- **JDK 11** or later
- **Android SDK** with API 26–34 installed
- A **Firebase project** with Authentication and Realtime Database enabled
- An internet connection (app requires Firebase at runtime)

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/Sheharyar45/Planetze.git
cd Planetze
```

### 2. Open in Android Studio

Open Android Studio and select **File → Open**, then navigate to the cloned directory.

### 3. Configure Firebase

See the [Firebase Setup](#-firebase-setup) section below.

### 4. Sync Gradle

Android Studio will automatically prompt you to sync Gradle. Click **Sync Now**, or run:

```bash
./gradlew build
```

### 5. Run the app

Select a device or emulator (API 26+) and click **Run ▶** in Android Studio, or:

```bash
./gradlew installDebug
```

---

## 🔥 Firebase Setup

1. Go to the [Firebase Console](https://console.firebase.google.com/) and create a new project (or use an existing one).

2. **Add an Android app** to your Firebase project:
   - Package name: `com.example.planetze86`
   - Download the generated `google-services.json` file.

3. Place `google-services.json` in the `app/` directory:
   ```
   app/
   └── google-services.json   ← place here
   ```

4. **Enable Authentication**:
   - In the Firebase Console go to **Authentication → Sign-in method**.
   - Enable **Email/Password**.

5. **Enable Realtime Database**:
   - In the Firebase Console go to **Realtime Database → Create database**.
   - Choose a region and start in **test mode** for development (lock down rules before production).
   - Recommended security rules:
     ```json
     {
       "rules": {
         "users": {
           "$uid": {
             ".read": "$uid === auth.uid",
             ".write": "$uid === auth.uid"
           }
         }
       }
     }
     ```

---

## 📁 Project Structure

```
Planetze/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/example/planetze86/
│   │   │   │   │
│   │   │   │   ├── ── Activities (UI Layer) ──
│   │   │   │   ├── MainActivity.java              # Launcher: Login / Register buttons
│   │   │   │   ├── LoginActivityView.java          # Login screen (MVP View)
│   │   │   │   ├── RegisterActivity.java           # Registration screen
│   │   │   │   ├── forgetPass.java                # Forgot password screen
│   │   │   │   ├── Onboarding.java                # First-time intro screen
│   │   │   │   ├── annualEmmision.java            # Annual questionnaire
│   │   │   │   ├── AnnualEmissionResult.java      # Questionnaire results screen
│   │   │   │   ├── PlanetzeMenu.java              # Main navigation menu
│   │   │   │   ├── EcoTracker.java                # Daily activity tracker
│   │   │   │   ├── TransportationTracking.java    # Transportation log
│   │   │   │   ├── FoodConsumptionTracking.java   # Food consumption log
│   │   │   │   ├── ShoppingTracking.java          # Shopping & energy log
│   │   │   │   ├── ecoGauge.java                  # Dashboard with charts
│   │   │   │   ├── userHabit.java                 # Eco habits browser
│   │   │   │   ├── ViewEmissionActivitiesActivity.java  # View & delete logs
│   │   │   │   │
│   │   │   │   ├── ── MVP (Auth) ──
│   │   │   │   ├── LoginActivityModel.java         # Auth & user data (Model)
│   │   │   │   ├── LoginActivityPresenter.java     # Login logic (Presenter)
│   │   │   │   │
│   │   │   │   ├── ── Domain Models ──
│   │   │   │   ├── EmissionActivityElement.java   # Abstract base for activities
│   │   │   │   ├── TransportationActivityElement.java
│   │   │   │   ├── FoodConsumptionActivityElement.java
│   │   │   │   ├── ShoppingActivityElement.java
│   │   │   │   ├── User.java                      # User data model
│   │   │   │   ├── Habit.java                     # Eco habit model
│   │   │   │   │
│   │   │   │   ├── ── Business Logic ──
│   │   │   │   ├── AnnualCalculator.java          # CO₂ calculation engine
│   │   │   │   ├── QuestionsRepo.java             # Onboarding question data
│   │   │   │   ├── Questions.java                 # Question model
│   │   │   │   ├── FirebaseManager.java           # Centralised Firebase access
│   │   │   │   │
│   │   │   │   └── utils/
│   │   │   │       └── FirebaseConstants.java     # Database path constants
│   │   │   │
│   │   │   ├── res/
│   │   │   │   ├── layout/       # 24 XML layout files
│   │   │   │   ├── drawable/     # 50+ icons, images, shape drawables
│   │   │   │   ├── mipmap/       # App launcher icons (all DPI variants)
│   │   │   │   ├── values/
│   │   │   │   │   ├── strings.xml     # All user-facing strings
│   │   │   │   │   ├── colors.xml      # Color palette (eco greens & blues)
│   │   │   │   │   └── themes.xml      # App theme (light/dark support)
│   │   │   │   └── raw/
│   │   │   │       ├── global.xlsx     # Country & global emission baselines
│   │   │   │       ├── formulas.xlsx   # Emission calculation formulas
│   │   │   │       └── habits_list.txt # Eco habits data
│   │   │   │
│   │   │   └── AndroidManifest.xml    # 14 declared Activities + permissions
│   │   │
│   │   ├── test/java/
│   │   │   └── ExampleUnitTest.java   # Mockito-based unit tests (login flow)
│   │   └── androidTest/java/
│   │       └── ExampleInstrumentedTest.java
│   │
│   ├── build.gradle.kts               # App-level dependencies
│   └── google-services.json           # Firebase config (not committed – add your own)
│
├── gradle/
│   ├── libs.versions.toml             # Dependency version catalog
│   └── wrapper/gradle-wrapper.properties
│
├── build.gradle.kts                   # Root-level Gradle config
├── settings.gradle.kts                # Gradle settings (includes JitPack)
└── gradle.properties                  # JVM/Gradle runtime config
```

---

## 🗺 User Flow

```
App Launch
    │
    ▼
MainActivity (Login / Register)
    │
    ├── Register → Email Verification → LoginActivityView
    │
    └── Login (LoginActivityView)
            │
            ├── First-time user ──────────► Onboarding → Annual Questionnaire
            │                                                      │
            │                                              AnnualEmissionResult
            │                                                      │
            └── Returning user ◄────────────────────────────────── ┘
                      │
                      ▼
               PlanetzeMenu
              ┌─────┼──────┐
              │     │      │
         EcoGauge  EcoTracker  Recalculate
              │     │
              │     ├── TransportationTracking
              │     ├── FoodConsumptionTracking
              │     └── ShoppingTracking
              │           │
              │     ViewEmissionActivities
              │
         userHabit (Eco Habits)
```

---

## 🧮 Emission Calculations

### Transportation

| Mode | Emission Factor |
|------|----------------|
| Personal vehicle – Gasoline | 0.24 kg CO₂/km |
| Personal vehicle – Diesel | 0.27 kg CO₂/km |
| Personal vehicle – Hybrid | 0.16 kg CO₂/km |
| Personal vehicle – Electric | 0.05 kg CO₂/km |
| Bus | Based on trip duration |
| Train / Subway | Based on trip duration |
| Short-haul flight | Fixed factor per flight |
| Long-haul flight | Fixed factor per flight |
| Cycling / Walking | 0 kg CO₂ |

### Food

| Meal type | Annual baseline | Per-meal emission |
|-----------|----------------|-------------------|
| Beef | 2500 kg CO₂/year | ÷ (365 × 2.5 meals/day) |
| Pork | 2500 kg CO₂/year | ÷ (365 × 2.5 meals/day) |
| Chicken | 1500 kg CO₂/year | ÷ (365 × 2.5 meals/day) |
| Fish | 1500 kg CO₂/year | ÷ (365 × 2.5 meals/day) |
| Plant-Based | 1000 kg CO₂/year | ÷ (365 × 2.5 meals/day) |

### Shopping & Energy

| Item | Emission |
|------|---------|
| Smartphone | 70 kg CO₂ per unit |
| Laptop | 200 kg CO₂ per unit |
| Tablet | 100 kg CO₂ per unit |
| TV | 300 kg CO₂ per unit |
| Clothing | 30 kg CO₂ per item |
| Gas bill | Cost × 5.3 |
| Electricity bill | Cost × 4.5 |
| Water bill | Cost × 1.5 |

### Annual Baseline
The onboarding questionnaire computes an annual CO₂ total and breaks it into four categories. These baselines are stored in Firebase and used as reference values in the Eco Gauge dashboard alongside country and global averages loaded from `global.xlsx`.

---

## 🌱 Eco Habits

18 built-in eco habits are grouped by category and impact level:

| Habit | Category | Impact |
|-------|----------|--------|
| Walk or Cycle | Transportation | 🔴 High |
| Drive instead of Flying | Transportation | 🔴 High |
| Carpool | Transportation | 🟡 Low |
| Use an Electric Car | Transportation | 🟠 Medium |
| Use Public Transport | Transportation | 🟠 Medium |
| Use Energy-Efficient Lightbulbs | Energy | 🔴 High |
| Unplug Devices When Not in Use | Energy | 🟠 Medium |
| Use Smart Thermostats | Energy | 🟠 Medium |
| Install Solar Panels | Energy | 🔴 High |
| Eat More Plant-Based Foods | Food | 🔴 High |
| Reduce Food Waste | Food | 🔴 High |
| Buy Local and Seasonal Produce | Food | 🟠 Medium |
| Compost Food Scraps | Food | 🟠 Medium |
| Buy Secondhand Items | Consumption | 🟠 Medium |
| Use Reusable Bags, Bottles, and Containers | Consumption | 🔴 High |
| Reduce, Reuse, Recycle | Consumption | 🔴 High |
| Support Sustainable Brands | Consumption | 🟠 Medium |
| Repair or Reuse Electronics Instead of Buying New | Consumption | 🟡 Low |

---

## 🧪 Testing

### Unit Tests (Mockito)

Located in `app/src/test/java/ExampleUnitTest.java`, covering the login flow with 11 test cases:

| Test | Scenario |
|------|---------|
| `testlogin_0` | Empty password field validation |
| `testlogin_1` | Forgot password button navigation |
| `testlogin_2` | Back button navigation |
| `testlogin_3` | Successful login → Onboarding (first-time user) |
| `testlogin_4` | Successful login → Main menu (returning user) |
| `testlogin_5` | Email not verified → Error message |
| `testlogin_6` | Firebase auth failure handling |
| `testlogin_7` | Signup button navigation |
| … | Additional validation scenarios |

### Instrumented Tests (Espresso)

Located in `app/src/androidTest/java/ExampleInstrumentedTest.java` – basic app context validation.

### Running Tests

```bash
# Unit tests
./gradlew test

# Instrumented tests (requires a connected device or emulator)
./gradlew connectedAndroidTest
```

---

## 🤝 Contributing

1. **Fork** the repository and create a feature branch:
   ```bash
   git checkout -b feature/your-feature-name
   ```
2. Make your changes following the existing code style (Java, MVP where applicable).
3. Add or update unit tests for any new logic.
4. Open a **Pull Request** describing your changes.

---

## 📄 License

This project was developed as part of the CSCB07 Software Design course. Please refer to your institution's academic integrity policies before reusing this code.
