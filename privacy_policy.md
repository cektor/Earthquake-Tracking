# Privacy Policy — Deprem (Earthquake Monitor)

**Package ID:** `com.alg.deprem`
**Last Updated:** June 2025
**Effective Date:** June 2025

---

## 1. Introduction

This Privacy Policy describes how the **Deprem – Earthquake Monitor** Android application ("the App", "we", "our") handles information on your device. The App is a real-time earthquake monitoring tool that fetches publicly available seismic data from official government and scientific institutions and displays it locally on your device.

**The App does not collect, store, transmit, share, sell, or process any personal data on any remote server.** All data processing happens exclusively on your device.

---

## 2. Summary — What We Do and Do Not Do

| Action | Status |
|---|---|
| Collect personal data | ❌ Never |
| Transmit personal data to any server | ❌ Never |
| Share data with third parties | ❌ Never |
| Sell data | ❌ Never |
| Use analytics or tracking SDKs | ❌ Never |
| Use advertising SDKs | ❌ Never |
| Create user accounts or profiles | ❌ Never |
| Store data in the cloud | ❌ Never |
| Access contacts, camera, microphone, or files | ❌ Never |
| Fetch publicly available earthquake data from official APIs | ✅ Yes — read-only, on-device only |
| Store fetched earthquake data in a local on-device database | ✅ Yes — never leaves your device |
| Store user preferences locally on your device | ✅ Yes — never leaves your device |
| Use device location to calculate distances to earthquakes | ✅ Optional — only when you explicitly enable it |

---

## 3. Earthquake Data Sources

The App fetches **publicly available, read-only seismic event data** from the following official institutions. These are outbound HTTP requests made by your device directly to the respective public APIs. No personal information is included in these requests.

### 3.1 EMSC — European-Mediterranean Seismological Centre
- **Full Name:** European-Mediterranean Seismological Centre
- **API Endpoint:** `https://www.seismicportal.eu/fdsnws/event/1/query`
- **Data Provided:** Global earthquake events — magnitude, location, depth, time, tsunami flag, region name
- **Coverage:** Worldwide
- **Protocol:** FDSN Web Services (standard seismological data protocol)
- **Privacy Policy:** [https://www.emsc-csem.org](https://www.emsc-csem.org)

### 3.2 AFAD — Disaster and Emergency Management Authority of Turkey
- **Full Name:** T.C. Afet ve Acil Durum Yönetimi Başkanlığı (Republic of Turkey)
- **API Endpoint:** `https://deprem.afad.gov.tr/apiv2/event/filter`
- **Data Provided:** Earthquake events in Turkey and surrounding regions — magnitude, coordinates, depth, time, location description
- **Coverage:** Turkey and surrounding region
- **Privacy Policy:** [https://deprem.afad.gov.tr](https://deprem.afad.gov.tr)

### 3.3 Kandilli Observatory — Boğaziçi University
- **Full Name:** Boğaziçi Üniversitesi Kandilli Rasathanesi ve Deprem Araştırma Enstitüsü
- **API Endpoint:** `https://api.orhanaydogdu.com.tr/deprem/kandilli/live` (community proxy API)
- **Original Source:** Kandilli Observatory and Earthquake Research Institute (KOERI)
- **Data Provided:** Recent earthquake events in Turkey — magnitude, coordinates, depth, time, location
- **Coverage:** Turkey and surrounding region
- **Original Source Website:** [http://www.koeri.boun.edu.tr](http://www.koeri.boun.edu.tr)

> **Important:** The App only reads data from these APIs. It does not send any user data, device identifiers, or location information to these APIs. API requests contain only query parameters such as time range, minimum magnitude, and geographic bounding box — all of which are derived from your local filter settings and never include any personally identifiable information.

---

## 4. Permissions Declared and Why They Are Required

The App declares the following Android permissions in its `AndroidManifest.xml`. Each permission is explained in full detail below.

---

### 4.1 `INTERNET`
**Classification:** Normal Permission (no user prompt required)

**Why it is required:**
The App must connect to the internet to fetch earthquake data from the three official seismic APIs listed in Section 3. Without this permission, the App cannot retrieve any earthquake information and would be entirely non-functional.

**What it is NOT used for:**
- Sending any user data to any server
- Analytics, advertising, or tracking
- Any background upload of any kind

---

### 4.2 `ACCESS_NETWORK_STATE`
**Classification:** Normal Permission (no user prompt required)

**Why it is required:**
The App checks whether a network connection is available before attempting to fetch earthquake data. This prevents unnecessary error states and battery drain when the device is offline. The App reads only the network connectivity status (connected/disconnected) — it does not read network names, MAC addresses, or any other network identifiers.

---

### 4.3 `ACCESS_FINE_LOCATION` and `ACCESS_COARSE_LOCATION`
**Classification:** Dangerous Permission — **requires explicit user consent at runtime**

**Why it is required:**
Location access is **entirely optional** and is only used for two specific user-initiated features:

1. **"Nearby Earthquakes" notification mode:** When you select the "Nearby" notification type in Settings, the App uses your current GPS location to calculate the distance between your position and each earthquake. Only earthquakes within your configured radius (default: 200 km) trigger a notification.

2. **"Use Current Location" button in Settings:** When you tap this button, the App reads your current GPS coordinates once and saves them locally on your device as your "custom location" for distance-based filtering. The coordinates are stored only in the app's local `SharedPreferences` file and are never transmitted anywhere.

**What happens if you deny location permission:**
The App continues to function fully. Earthquake lists, maps, and all other notification modes (by country, continent, worldwide, custom coordinates) work without location access. Only the "Nearby" notification mode and the "Use Current Location" button require this permission.

**Location data is:**
- Never transmitted to any server
- Never shared with any third party
- Never stored outside your device
- Only used on-device for distance calculations (Haversine formula)
- Only accessed when you explicitly trigger a location-dependent feature

---

### 4.4 `POST_NOTIFICATIONS`
**Classification:** Dangerous Permission — **requires explicit user consent at runtime (Android 13+)**

**Why it is required:**
The App's core safety feature is earthquake alert notifications. When a new earthquake matching your configured criteria (magnitude threshold, geographic scope) is detected, the App sends a local notification to your device. This permission is required on Android 13 (API 33) and above to display any notifications.

**Notification types sent:**
- **Monitoring status notification** (silent, ongoing): Indicates the background monitoring service is active. Displayed in the notification shade with minimum priority.
- **Earthquake warning notification** (M3.0–M4.9): High-priority notification with vibration and sound.
- **Earthquake alarm notification** (M5.0+): Maximum-priority notification that can bypass Do Not Disturb mode (see Section 4.6), with full-screen intent on lock screen.

**All notifications are generated entirely locally on your device.** No push notification service (Firebase Cloud Messaging, etc.) is used. No notification content is sent to or received from any remote server.

---

### 4.5 `ACCESS_NOTIFICATION_POLICY`
**Classification:** Special Permission — requires user to grant via system settings

**Why it is required:**
For earthquakes of magnitude M5.0 and above, the App is designed to alert you even if your device is in Do Not Disturb (DND) / Silent mode. This is a critical safety feature — a major earthquake may occur while your phone is silenced at night. This permission allows the App's "Earthquake Alarm" notification channel to bypass DND restrictions.

**This permission is only used to:**
- Allow the dedicated "Earthquake Alarm" notification channel (`deprem_alarm`) to bypass DND for M5.0+ events
- The channel is configured with `setBypassDnd(true)` and `AudioAttributes.USAGE_ALARM`

**This permission is NOT used to:**
- Read your DND schedule or settings
- Modify your DND policy in any way
- Access any other notification-related data

---

### 4.6 `USE_FULL_SCREEN_INTENT`
**Classification:** Special Permission (restricted on Android 14+, requires declaration)

**Why it is required:**
For critical earthquakes (M5.0+), the App launches a full-screen alarm activity (`EarthquakeAlarmActivity`) that appears on the lock screen, similar to an incoming call screen. This ensures that a major earthquake alert is immediately visible even when the device screen is off or locked.

**This is used exclusively for:**
- Displaying the `EarthquakeAlarmActivity` when a M5.0+ earthquake is detected
- The activity shows magnitude, location, depth, time, and tsunami warning status

**On Android 14 (API 34) and above**, the App checks `NotificationManager.canUseFullScreenIntent()` before using this feature, respecting the system-level permission grant.

---

### 4.7 `FOREGROUND_SERVICE` and `FOREGROUND_SERVICE_DATA_SYNC`
**Classification:** Normal Permission / Special Declaration

**Why it is required:**
The App runs a foreground service (`EarthquakeMonitoringService`) that polls the selected earthquake API every 30 seconds to check for new events. Android requires that any service running in the foreground (visible to the user via a persistent notification) declare the `FOREGROUND_SERVICE` permission.

`FOREGROUND_SERVICE_DATA_SYNC` is the specific foreground service type required on Android 14+ for services that perform network data synchronization. The service is declared with `android:foregroundServiceType="dataSync"` in the manifest.

**The foreground service:**
- Runs only when earthquake monitoring is enabled by the user
- Displays a persistent, silent "Earthquake Monitoring Active" notification while running
- Polls the selected API every 30 seconds
- Processes all data locally and only sends local notifications
- Does not transmit any data to any server

---

### 4.8 `RECEIVE_BOOT_COMPLETED`
**Classification:** Normal Permission (no user prompt required)

**Why it is required:**
When your device restarts, Android terminates all running services. The `BootReceiver` broadcast receiver listens for the `ACTION_BOOT_COMPLETED` system broadcast and automatically restarts the earthquake monitoring service after a device reboot. This ensures continuous earthquake monitoring without requiring you to manually reopen the App after every restart.

**This permission is NOT used to:**
- Access any data at boot time
- Perform any action other than starting the monitoring service

---

### 4.9 `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS`
**Classification:** Special Permission

**Why it is required:**
Android's battery optimization (Doze mode) can delay or kill background services and scheduled tasks. For an earthquake monitoring app, timely delivery of alerts is critical — a 15-minute delay in a M7.0 earthquake notification could be life-threatening. This permission allows the App to request exemption from battery optimization so that the 30-second polling interval remains reliable.

**The user is always in control:** Android presents a system dialog asking the user to confirm this exemption. The App cannot grant itself this exemption without explicit user approval.

---

### 4.10 `WRITE_EXTERNAL_STORAGE` (maxSdkVersion="29")
**Classification:** Dangerous Permission — **only applies to Android 9 (API 29) and below**

**Why it is required:**
The App uses **OSMDroid** (OpenStreetMap for Android) to display the interactive earthquake map. On Android 9 and below, OSMDroid requires write access to external storage to cache map tiles locally, which significantly improves map performance and reduces data usage by avoiding re-downloading the same map tiles.

**This permission:**
- Is declared with `android:maxSdkVersion="29"` — it is **not requested on Android 10 or above**
- Is used exclusively by the OSMDroid library for map tile caching
- Only writes map tile image files to the OSMDroid cache directory
- Does not read, write, or access any personal files

---

## 5. Data Stored Locally on Your Device

The App stores the following data **exclusively on your device**. None of this data is ever transmitted to any server or accessible by any third party.

### 5.1 Local Database (Room / SQLite)
**File:** Internal app storage — `depremler` table
**Contents:** Earthquake event records fetched from the selected API, including:
- Earthquake ID (assigned by the source API)
- Magnitude and magnitude type
- Latitude and longitude
- Depth (km)
- Timestamp (epoch milliseconds)
- Location description and region name
- Country (if provided by API)
- Tsunami flag
- Data source identifier (EMSC / AFAD / KANDILLI)
- Internal flag: whether a notification has been sent for this event

**Retention:** Records older than 30 days are automatically deleted from the local database.

**Backup policy:** The local earthquake database is **excluded** from Android cloud backup (`backup_rules.xml` and `data_extraction_rules.xml` both exclude the `database` domain). This means earthquake data is never uploaded to Google Drive or any cloud backup service.

### 5.2 SharedPreferences (App Settings)
**File:** `deprem_ayarlari` (internal app storage)
**Contents:** User preferences including:
- Selected notification type (none / nearby / custom distance / my country / my continent / worldwide)
- Minimum magnitude threshold for notifications
- Minimum magnitude threshold for list display
- Custom location coordinates (latitude/longitude) — only if you set them manually
- Custom location name
- Custom notification radius (km)
- Nearby notification radius (km)
- Selected country for country-based notifications
- Selected continent for continent-based notifications
- Timestamp of last notification sent
- Active list filter settings (country, region, time range)
- Automatic location-based list filter toggle
- Fault lines display toggle
- Selected earthquake data source (EMSC / AFAD / KANDILLI)

**Backup policy:** SharedPreferences are included in Android cloud backup. This means your app settings (but NOT earthquake data) may be backed up to your Google account's app backup service, which is managed entirely by Android and Google. The App does not control or initiate this backup — it is a standard Android feature. No location coordinates or sensitive data beyond app configuration preferences are included.

---

## 6. Third-Party Libraries and SDKs

The App uses the following open-source libraries. None of these libraries collect personal data or transmit any information to third-party servers in the context of this App.

| Library | Purpose | Data Collection |
|---|---|---|
| Retrofit 2 | HTTP client for API requests | None |
| OkHttp 3 | Underlying HTTP engine | None |
| Gson | JSON parsing | None |
| Room (AndroidX) | Local SQLite database | None |
| OSMDroid 6 | OpenStreetMap interactive map | Tile requests to OpenStreetMap tile servers (anonymous, no personal data) |
| Google Play Services Location | GPS/network location provider | Used only on-device; no data sent to Google beyond standard OS location services |
| Hilt (Dagger) | Dependency injection | None |
| WorkManager (AndroidX) | Background task scheduling | None |
| Kotlin Coroutines | Asynchronous programming | None |
| AndroidX Navigation | In-app navigation | None |
| Material Components | UI components | None |

### OSMDroid Map Tiles
When you view the map screen, OSMDroid loads map tiles from **OpenStreetMap tile servers** (`tile.openstreetmap.org`). These are standard anonymous HTTP requests for map image tiles. Your IP address is visible to the tile server as with any web request, but no personal data, location, or app-specific information is included. OpenStreetMap's privacy policy applies: [https://wiki.osmfoundation.org/wiki/Privacy_Policy](https://wiki.osmfoundation.org/wiki/Privacy_Policy)

### Google Play Services Location
The Google Play Services Location API (`FusedLocationProviderClient`) is used to obtain your device's current GPS coordinates when you explicitly request it (see Section 4.3). This is a standard Android system service. The App does not send your location to Google — it only reads the location value returned by the system API and stores it locally.

---

## 7. Children's Privacy

The App does not knowingly collect any information from anyone, including children under the age of 13. Since the App collects no personal data whatsoever, it is safe for use by all age groups. The App contains no advertising, no in-app purchases, and no social features.

---

## 8. Security

Since the App does not transmit any personal data to any server, the risk of a data breach involving personal information is zero by design. All data remains on your device and is protected by Android's standard application sandboxing.

- All API communications use **HTTPS** (TLS). The App sets `android:usesCleartextTraffic="false"` in the manifest, which means unencrypted HTTP connections are blocked at the OS level.
- The local database and SharedPreferences are stored in the App's private internal storage directory, which is not accessible to other apps on non-rooted devices.

---

## 9. Data Retention

| Data Type | Location | Retention Period |
|---|---|---|
| Earthquake records | Local SQLite database | 30 days (auto-deleted) |
| App settings / preferences | Local SharedPreferences | Until app is uninstalled |
| Map tile cache | External storage (Android ≤9) / App cache | Managed by OSMDroid |

Uninstalling the App removes all locally stored data from your device.

---

## 10. Your Rights and Controls

Since the App does not collect or process any personal data on any server, formal data subject rights (access, deletion, portability) under regulations such as GDPR or CCPA do not apply in the traditional sense — there is no data held about you anywhere other than on your own device.

You have full control over all data on your device:

- **Clear app data:** Settings → Apps → Deprem → Storage → Clear Data. This deletes all earthquake records and resets all preferences.
- **Revoke location permission:** Settings → Apps → Deprem → Permissions → Location → Deny. The App continues to function without location access.
- **Revoke notification permission:** Settings → Apps → Deprem → Notifications → Disable. Earthquake alerts will no longer be delivered.
- **Uninstall the App:** Removes all locally stored data permanently.

---

## 11. Changes to This Privacy Policy

If we make material changes to this Privacy Policy, we will update the "Last Updated" date at the top of this document and publish the updated policy. Continued use of the App after changes are posted constitutes acceptance of the updated policy.

---

## 12. Contact

If you have any questions or concerns about this Privacy Policy or the App's data practices, please contact:

**Developer:** ALG Software
**Email:** *(provide your contact email here)*
**GitHub:** *(provide your repository URL here)*

---

## 13. Compliance Statement

This App is designed and built in full compliance with:

- [Google Play Developer Program Policies](https://play.google.com/about/developer-content-policy/)
- [Google Play Families Policy](https://play.google.com/about/families/)
- [EU General Data Protection Regulation (GDPR)](https://gdpr.eu/) — Not applicable as no personal data is processed
- [California Consumer Privacy Act (CCPA)](https://oag.ca.gov/privacy/ccpa) — Not applicable as no personal data is collected or sold
- [Android Permission Best Practices](https://developer.android.com/training/permissions/usage-notes)

> This privacy policy was generated based on a complete source code review of the application. All permission justifications are derived directly from the application's `AndroidManifest.xml`, Kotlin source files, and library dependencies.
