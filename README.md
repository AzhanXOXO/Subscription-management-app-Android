# Due

**A calm, private Android app for staying ahead of free trials and subscription renewals.**

Due reminds you before a subscription charges, gives you time to decide whether it is still worth keeping, and keeps your recurring spending visible without connecting to your bank or creating an account.

<p align="center">
  <img src="docs/due-preview.png" alt="Due subscription dashboard showing upcoming renewals and a trial-ending reminder" width="360">
</p>

> [!IMPORTANT]
> Due is an early personal-testing release. The reminder logic and interface have automated coverage, but notification delivery still needs verification on physical Android devices before the app should be relied on for important renewals.

## Why Due?

Free trials are easy to forget, and ordinary notifications are easy to dismiss. Due creates an intentional review point before a charge:

1. Add a subscription or free trial.
2. Choose how early and at what time Due should remind you.
3. Review, snooze, or keep the subscription when the notification appears.
4. Cancel directly with the provider, then mark it cancelled in Due to stop future reminders.

Due never charges cards or cancels subscriptions itself. It gives you enough notice to make that decision with the provider.

## Features

- Custom reminders from 0–90 days before a renewal
- Persistent notifications with sound and vibration
- Notification actions for **Review**, **Snooze 1h**, and **Keep**
- Optional daily follow-ups through the renewal date
- Free-trial shortcuts for 7, 14, and 30 days
- Monthly, yearly, weekly, and custom billing cycles
- Upcoming-renewal dashboard and renewal calendar
- Search and active, trial, and cancelled filters
- Monthly commitment estimate and optional spending budget
- Support for USD, PKR, EUR, GBP, INR, AED, CAD, AUD, and SAR
- Light, dark, and system themes
- Categories, notes, provider-management links, and decision history
- Local JSON backup and restore
- Reminder recovery after reboot, app update, clock changes, and time-zone changes

## Privacy

Due is designed to work offline:

- No account or sign-in
- No ads or analytics
- No bank or email access
- No server-side database
- No Internet permission
- Subscription data stays on the device

Backups contain the subscription information you enter, so save exported backup files somewhere private. Provider links are opened externally in your browser.

## Install the Android app

Due requires **Android 8.0 (API 26) or newer**.

1. Download the latest APK from the repository's **Releases** page.
2. Open the APK from your phone's Files app.
3. If Android asks, allow your Files app or browser to install this APK.
4. Open Due and allow notifications.
5. Open **Settings → Allow precise reminders** and enable Due's alarm access when available.
6. Tap **Test a notification**, background the app, and confirm that the notification appears with sound.

The current APK is development-signed and intended for personal testing. Android may display an unknown-source warning because the app is not distributed through Google Play.

## Using a seven-day trial

Create a subscription, select **Free trial**, enter the price that will apply after the trial, and tap **7-day trial from today**. By default, Due schedules the reminder two days before the first charge at 09:00 in the phone's local time.

You can change the lead time and reminder hour for each subscription. An earlier reminder is safer than a same-day reminder because some providers charge early on the renewal date.

When the reminder appears:

- **Review** opens the subscription in Due.
- **Snooze 1h** postpones the reminder by one hour. The app also offers 3-hour and 24-hour options.
- **Keep** acknowledges the current renewal. Due prepares the next reminder after the billing date.
- If you cancel through the provider, choose **I've cancelled with the provider** in Due to stop its reminders.

Unreviewed overdue subscriptions remain visible instead of being silently advanced to a later billing date.

## Technology

Due uses a small hybrid Android architecture:

| Layer | Technology | Responsibility |
| --- | --- | --- |
| Android runtime | Java | Notifications, alarms, permissions, reboot recovery, backups, and secure local persistence |
| Application logic | JavaScript | Subscription workflows, renewal calculations, validation, search, budget, and calendar behavior |
| Interface | HTML and CSS | Responsive dashboard, forms, calendar, themes, and accessible controls |
| Build | Gradle/Groovy | Android project and dependency configuration |

The interface runs in a restricted Android WebView. Native Java components schedule and deliver reminders even when the interface is closed. The WebView serves only bundled application files from a synthetic local HTTPS origin and blocks external navigation inside the app.

## Project structure

```text
Due/
├── android/                         Android Studio project
│   └── app/src/main/
│       ├── assets/                  HTML, CSS, and JavaScript interface
│       ├── java/app/due/subscriptions/
│       │   ├── MainActivity.java    WebView host and Android bridge
│       │   ├── Reminders.java       Alarm and notification handling
│       │   ├── ScheduleMath.java    Recurring-date calculations
│       │   └── Store.java           Local data storage and validation
│       └── res/                     Android styles and icons
├── tests/                           JavaScript and Java checks
├── DEVICE-CHECKLIST.md              Physical-device verification plan
├── build.ps1                        Dependency-free APK build helper
└── preview.cjs                      Local interface preview
```

## Build from source

### Android Studio

1. Install Android Studio with **JDK 17** and **Android SDK Platform 36**.
2. Open the `android` directory as a project.
3. Allow the included Gradle wrapper to download Gradle 8.11.1.
4. Run the `app` configuration on an Android device, or select **Build → Build APK(s)**.

The project uses Android Gradle Plugin 8.10.1 and targets Android API 36.

### PowerShell build

A dependency-free build helper is included for Windows:

```powershell
.\build.ps1 `
  -JavaHome 'C:\path\to\jdk-17' `
  -AndroidJar 'C:\path\to\sdk\platforms\android-36\android.jar' `
  -BuildTools 'C:\path\to\sdk\build-tools\36.0.0'
```

The script creates a local development signing key when one is not supplied. Never commit signing keys. Use a dedicated, securely stored release key for distributed builds and keep using the same key for future updates.

## Preview the interface

The browser preview needs Node.js but has no package dependencies:

```bash
npm run preview
```

Then open `http://127.0.0.1:4173/phone`.

The preview supports the interface and browser-local storage. Native background notifications, Android permissions, reboot recovery, and Android's file picker require the APK.

## Tests

Run the JavaScript checks with:

```bash
npm test
```

The current test suite covers:

- Seven-day trial reminder dates
- Monthly, yearly, weekly, and custom recurrence
- Month-end and leap-year billing anchors
- Reminder lead times and local hours
- Snooze and daily follow-up behavior
- Daylight-saving transitions
- Cost normalization
- Backup and input validation, including malformed and unsafe values

The latest verification completed **25 JavaScript checks** and **15 native Java scheduling checks** successfully. The APK was compiled against Android SDK 36 and passed Android v2 and v3 signature verification.

Physical-device testing is still required for installation, permission screens, background delivery, notification actions, reboot recovery, and backup file-picker flows. See [DEVICE-CHECKLIST.md](DEVICE-CHECKLIST.md) for the checklist.

## Notification limitations

Android ultimately controls notification sound, vibration, visibility, and timing. Delivery can be affected by silent mode, Do Not Disturb, battery optimization, manufacturer power-management rules, or force-stopping the app. If precise-alarm access is unavailable, Due falls back to flexible delivery and Android may delay the notification.

This release does not include a full-screen ringing alarm, phone calls, SMS, automatic provider cancellation, automatic bank or email imports, cloud sync, widgets, or a Play Store listing.

## Roadmap

- Complete testing across physical Android devices and manufacturers
- Add notification and accessibility instrumentation tests
- Prepare reproducible signed releases and release notes
- Add home-screen widgets
- Add optional encrypted device-to-device sync
- Explore opt-in import helpers without requiring bank credentials

## Contributing

Bug reports and focused pull requests are welcome. When reporting a reminder problem, include the Android version, phone manufacturer, whether precise alarms were enabled, and whether battery optimization was active. Do not include private subscription or account details.

Before opening a pull request, run the JavaScript tests and complete the relevant items in the device checklist.

## License

No open-source license has been selected yet. Until a license file is added, the source is publicly viewable but is not automatically licensed for reuse, modification, or redistribution.

## Android references

- [Schedule alarms](https://developer.android.com/develop/background-work/services/alarms)
- [Notification runtime permission](https://developer.android.com/develop/ui/views/notifications/notification-permission)
- [Android SDK tools](https://developer.android.com/tools)
