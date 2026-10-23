# tv-diag-apk

Android TV performance diagnostic. The TV app measures and serves a small dashboard; Gil opens it on a phone over the home Wi-Fi.

Releases: v1.1 is current (APK asset name has a numeric prefix added by the GitHub uploader). Source: 3-tv-diag-source.zip (Gradle project in app-project/, Paparazzi screenshot test in verify/, mock-data dashboard server and JVM test in tools/).

## Build
JDK 17, Android SDK platform 34 + build-tools 34.0.0, Gradle 8.5. Set env KS_FILE (keystore path) and KS_PASS, key alias tvdiag, then run: gradle assembleRelease. Package com.instinct.tvdiag, minSdk 26, targetSdk 29. Sources are UTF-8 (Hebrew strings): keep file.encoding=UTF-8.
The signing key is NOT in this repo. Updates must be signed with the same key or Android refuses to update in place.

## What is measured
Ordinary app, no extra setup: system memory (MemAvailable, low-memory flag, swap), storage, thermal status, CPU info if readable, installed-app list with background-capability flags (not memory).
With usage access (adb appops set com.instinct.tvdiag GET_USAGE_STATS allow): foreground app per sample, 7-day foreground time, per-app storage size.
Per-app RAM: an ordinary app cannot read it on Android 10. The dashboard has a paste tool for the output of adb shell dumpsys meminfo taken from a computer. The in-app dumpsys attempt (needs DUMP + PACKAGE_USAGE_STATS + usage appop) is experimental and probably blocked by SELinux on Android 10 (meminfo is a system_api_service in android-10.0.0_r47 sepolicy; not proven).

## Test provenance (what was and was not verified)
- Built and signed; apksigner and aapt2 badging checked.
- JVM tests: dumpsys meminfo parser against a sample written from the known format (not a real capture), web server PIN auth (401/200/404/429).
- Phone dashboard rendered in headless Chrome with MOCK data (tools/mock_dashboard_server.py). TV screen rendered with Paparazzi layoutlib, not a real TV.
- NOT tested: install and run on a real TV (Mi TV, Android 10), foreground service during standby, readable /proc and sysfs files, usage stats and storage stats on the TV, dumpsys under granted permissions, phone-to-TV reachability.
