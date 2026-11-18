# ParkAssist — Mobile Application (React Native / Expo)

Cross-platform mobile application for **ParkAssist**, an intelligent, privacy-preserving vehicle incident reporting and multi-channel owner alert platform.

Built with **React Native** and **Expo**, the mobile app provides vehicle owners with an intuitive interface to manage vehicles, generate privacy-preserving QR stickers, receive instant real-time incident reports, and manage resolutions. It also includes an administrative management console for platform operators.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation & Setup](#installation--setup)
- [Backend Network Configuration](#backend-network-configuration)
  - [Environment Variables (.env)](#environment-variables-env)
  - [In-App Dynamic Server Switcher](#in-app-dynamic-server-switcher)
  - [Device-Specific IP Routing](#device-specific-ip-routing)
- [Available Scripts](#available-scripts)
- [App Walkthrough & Workflow](#app-walkthrough--workflow)
- [Security & Privacy Design](#security--privacy-design)
- [Troubleshooting](#troubleshooting)

---

## Features

- **Owner Authentication & Account Management:**
  - Secure signup and login with JWT token session handling.
  - Multi-field registration (Name, Email, Phone number, Password).
  - Role-aware interface (standard Vehicle Owner vs. Administrator).

- **Real-Time Fleet Dashboard:**
  - At-a-glance metrics: Total registered vehicles, active incident reports, and resolved issues.
  - Recent incident timeline with status badges (Pending, In Progress, Resolved).
  - Quick action shortcuts for fast navigation.

- **Vehicle Management & QR Sticker Generation:**
  - Register vehicles with license plate, make, model, manufacturing year, color, and vehicle category (Car, SUV, Motorcycle, Truck, Van).
  - Instant on-device rendering of crisp vector QR codes using `react-native-qrcode-svg`.
  - Privacy-preserving QR code embeds an opaque public token (`vk_*`) linking to `/report/<token>`.
  - Share, copy token, or preview the public web reporter interface directly from the app.

- **Incident Alerting & Management:**
  - Real-time in-app background polling for incoming incident reports.
  - Instant alert dialogs notify owners when an incident is submitted against their vehicle.
  - Filter incidents by status (`All`, `Pending`, `In Progress`, `Resolved`).
  - View full report context including incident type (wrong parking, damage, headlights on, blocking exit), descriptions, timestamps, photo attachments, and GPS coordinates.
  - Status updates: Acknowledge and resolve incidents directly from the report card.

- **Vehicle Database Search:**
  - Search registered vehicles across the system by plate number, make, or model.
  - Filter by vehicle body type.

- **Administrative Console:**
  - System statistics: Overview of total users, vehicles, and active vs. resolved incidents.
  - Fleet inspection: View and audit all registered vehicles across all owners.
  - Incident moderation: Monitor all platform incidents with moderation tools.
  - Account and record deletion with confirmation dialogs.

- **Dynamic Server URL Configurator:**
  - In-app modal accessible from the top navigation bar to switch the backend API URL on the fly without editing code or rebuilding the application.

---

## Tech Stack

| Component | Technology | Version / Details |
| :--- | :--- | :--- |
| **Framework** | [React Native](https://reactnative.dev/) / [Expo](https://expo.dev/) | Expo SDK 58, React Native 0.88.0, React 19.3.0 |
| **Language** | JavaScript (ES6+ / JSX) | Modern hooks & async state management |
| **Vector QR Engine** | `react-native-qrcode-svg` + `react-native-svg` | Scalable vector SVG QR rendering |
| **Asset & Status Bar** | `expo-asset`, `expo-status-bar` | Native device adaptation |
| **Target Platforms** | Android, iOS, Web | Universal cross-platform deployment |

---

## Project Structure

```text
mobile/
├── .expo/                   # Expo CLI cache and build artifacts
├── .env.example             # Template for API endpoint environment variable
├── .gitignore               # Ignored dependencies, build outputs, and env files
├── App.js                   # Main application component, state, navigation & views
├── app.json                 # Expo configuration (name, slug, orientation, splash, bundle ID)
├── package.json             # NPM package manifest, scripts, and dependencies
└── package-lock.json        # Deterministic dependency lockfile
```

---

## Prerequisites

Before running the mobile application, ensure you have:

1. **Node.js**: LTS version (v18.x or v20.x+) installed.
2. **Package Manager**: `npm` (bundled with Node) or `yarn`.
3. **Expo Go** or **Emulator**:
   - **Physical Device:** Install **Expo Go** from Google Play Store (Android) or Apple App Store (iOS).
   - **Android Emulator:** Android Studio with an active Virtual Device (AVD).
   - **iOS Simulator:** Xcode on macOS.
4. **ParkAssist Backend API**: Running on your local machine or hosted remotely (see `../backend/README.md`).

---

## Installation & Setup

### 1. Install Dependencies

Navigate to the `mobile` directory and install the packages:

```bash
cd mobile
npm install
```

### 2. Configure Environment

Create your local `.env` file from the provided example:

```bash
# On Windows PowerShell:
Copy-Item .env.example .env

# On macOS/Linux:
cp .env.example .env
```

Open `.env` and set your backend API URL:

```env
# Example for testing with a physical device on the same local Wi-Fi:
EXPO_PUBLIC_API_URL=http://192.168.1.50:5000/api
```

### 3. Start the Development Server

Launch the Expo development server:

```bash
npm start
```

This starts the Metro bundler and displays an interactive terminal menu and a QR code:
- **Physical Phone:** Scan the QR code using the **Expo Go** app (Android) or the native Camera app (iOS).
- **Android Emulator:** Press `a` in the terminal to launch on the connected emulator.
- **Web Browser:** Press `w` to run in a web browser.

---

## Backend Network Configuration

Because the mobile app connects to the Flask backend over HTTP, configuring the API address correctly is essential depending on your execution environment.

### Environment Variables (.env)

The application reads `process.env.EXPO_PUBLIC_API_URL`. When set, it becomes the default base URL for all network requests.

### In-App Dynamic Server Switcher

You do not need to restart Expo if your IP changes. In the top-right header of the **Login**, **Sign Up**, or **Main Dashboard** screens, tap the **Settings / Server Gear** icon. You can:
1. Enter any HTTP/HTTPS address (e.g. `http://192.168.1.100:5000/api`).
2. Save changes immediately for active API calls.

### Device-Specific IP Routing

| Environment | API URL Format | Why? |
| :--- | :--- | :--- |
| **Android Emulator** | `http://10.0.2.2:5000/api` | `10.0.2.2` is the special alias mapped to the host development machine's loopback interface. |
| **iOS Simulator** | `http://localhost:5000/api` or `http://127.0.0.1:5000/api` | The iOS simulator shares the network interface of macOS directly. |
| **Physical Phone (Expo Go)** | `http://<YOUR_COMPUTER_LAN_IP>:5000/api` | `localhost` on a real phone refers to the phone itself. Both the phone and PC must be on the **same Wi-Fi network**. |
| **Production / Cloud** | `https://your-domain.com/api` | Publicly reachable HTTPS address. |

> **Tip (Finding your LAN IP):**
> - **Windows:** Run `ipconfig` in PowerShell and look for `IPv4 Address` under your active Wi-Fi adapter (e.g. `192.168.1.X`).
> - **macOS/Linux:** Run `ifconfig` or `ip a` and check your Wi-Fi interface (`en0` / `wlan0`).

---

## Available Scripts

In the `mobile` directory, you can run:

| Command | Action |
| :--- | :--- |
| `npm start` | Starts the Expo development server with the interactive CLI. |
| `npm run android` | Starts Expo and automatically opens the app on a running Android emulator or connected USB device. |
| `npm run web` | Starts Expo with Metro bundler configured for web preview in your default browser. |

---

## App Walkthrough & Workflow

```
[ Register / Login ] ──> [ Dashboard ]
                               │
       ┌───────────────────────┼────────────────────────┐
       ▼                       ▼                        ▼
[ Add Vehicle ]        [ My Vehicles ]          [ Incident Reports ]
  Input plate &         - View Fleet             - Filter by status
  specifications        - High-Res QR Modal      - Review incident photos & GPS
       │                - Share QR / Copy Token  - Mark Acknowledged / Resolved
       ▼
 [ Backend generates ]
 [ public token vk_* ]
```

1. **Sign Up / Login:** Create an account or sign in with registered credentials.
2. **Add a Vehicle:** Input license plate, make, model, year, and category. The backend issues an opaque public vehicle token (`vk_*`).
3. **Inspect & Print QR Code:** Open the vehicle card, tap **View QR Code**, and test scanning it.
4. **Simulate a Public Report:** Open the public URL `/report/<token>` in any mobile or desktop browser to submit an anonymous parking or damage report.
5. **Receive Real-Time Alert:** The mobile app detects the new incident within seconds and prompts the owner with an incident notification.
6. **Take Action:** Navigate to the **Reports** tab, inspect details (time, note, photo), and update the status to **Resolved**.

---

## Security & Privacy Design

- **Zero Exposure of Owner Identity:** The QR code and token encode only a randomized identifier (`vk_*`). Neither the owner's phone number, name, nor email are present in the QR code or the public API response.
- **Authenticated Access:** All management endpoints require an `Authorization: Bearer <JWT>` token.
- **Admin Isolation:** Admin controls (user audits, vehicle deletions, platform stats) are restricted to authenticated accounts with administrative privileges.

---

## Troubleshooting

- **`Network request failed` / `Fetch failed`:**
  - Verify that the backend is running (`curl http://localhost:5000/api/health`).
  - If using a physical phone, ensure your phone and computer are on the same Wi-Fi network.
  - Make sure your computer's firewall is not blocking incoming connections on port `5000`.
  - Check the API URL in the app's top-right Server Config modal.

- **QR Code Not Rendering:**
  - Ensure `react-native-svg` and `react-native-qrcode-svg` are installed (`npm install`).
  - Clear Metro bundler cache by starting Expo with cache reset: `npx expo start -c`.

- **Metro Cache / Stale Build:**
  ```bash
  npx expo start --clear
  ```

