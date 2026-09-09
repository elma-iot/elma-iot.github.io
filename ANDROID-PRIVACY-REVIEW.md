# Android privacy findings and Play Data Safety worksheet

Reviewed 9 September 2026. Android 1.0.0 / version code 1, `com.elma.iot.flasher`, source revision `d4c2760253ac25fe52826f4ad333c49ceb252d9f`. No Android source was changed or copied into this public website repository.

## Source findings

| Area | Current behavior and evidence |
| --- | --- |
| App permissions | Main manifest declares INTERNET, ACCESS_NETWORK_STATE, ACCESS_WIFI_STATE, CHANGE_WIFI_STATE, ACCESS_FINE_LOCATION, ACCESS_COARSE_LOCATION, FOREGROUND_SERVICE, FOREGROUND_SERVICE_DATA_SYNC and FOREGROUND_SERVICE_CONNECTED_DEVICE. |
| SDK permissions | Merged release manifest additionally declares com.android.vending.BILLING, com.google.android.gms.permission.AD_ID, ACCESS_ADSERVICES_AD_ID, ACCESS_ADSERVICES_ATTRIBUTION, ACCESS_ADSERVICES_TOPICS, WAKE_LOCK and the app's signature-protected DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION. A permission is not proof of an actual transfer. |
| Wi-Fi/location | AndroidNetwork requests fine/coarse location interactively for WifiManager scans, reads SSID/RSSI/frequency/security and current network/IP. No GPS coordinate or background location requests. Raw scan list stays local; selected SSID can travel in configuration. No BSSID is copied into the returned JSON. |
| Bluetooth/nearby | No Bluetooth/nearby permissions or Android Bluetooth API flow. Hardware Designer Bluetooth choices describe peripherals, not phone access. |
| Camera/microphone/photos | No camera, recording, contacts, SMS or media-library permissions. No phone capture flow found. |
| Files | MainActivity uses ACTION_OPEN_DOCUMENT/ACTION_CREATE_DOCUMENT and a WebView file chooser. Configuration JSON and firmware bundle imports are explicit selections. Device pages can offer selected file/media uploads. No broad filesystem scan. |
| Accounts | No ELMA account creation, login, email/password backend or social login. Device HTTP credentials, PC token and MQTT login authenticate to those user-selected destinations. Billing can use an existing Google Play account. |
| AdMob/UMP | Mobile Ads 25.4.0 and UMP 4.0.0 included. Gradle hard-codes productionAdsEnabled=false, yielding LIVE_MONETIZATION=false. Monetization blocks ad initialization/loading and normal consent updates with that flag; test IDs and delayed measurement initialization remain. Privacy-options menu still calls UMP. Merged manifest retains MobileAdsInitProvider and advertising permissions. Source does not establish zero SDK background traffic. |
| Billing | Billing 9.1.0 client starts on app creation, queries purchases on setup/resume/restore. Checkout is blocked by LIVE_MONETIZATION=false. Verification uses product/package/RSA public key; a verified existing purchase can be acknowledged and cached. Default missing public key prevents verification. No developer purchase server, card processing or account email access in app code. |
| Analytics/crashes | No separate Firebase Analytics, Crashlytics or Sentry integration. Paho logging is discarded; WebView console messages are suppressed. Provider SDK diagnostics are a separate issue and must not be ruled out by this finding. |
| IDs | Random temporary MQTT client ID; USB numeric device/vendor/product IDs and chip/flash details; configured ESP identity and device status; OTA random session ID. No direct IMEI, Android ID or phone advertising-ID read found in app-owned code. Google SDKs include advertising/app-set ID support. |
| PC compilation | AndroidJobsBridge stores paired URL, certificate fingerprint and token; validates pinned HTTPS; sends complete settings to /v1/build, authenticates using bearer token and receives job logs/bundle. No fixed ELMA-hosted compiler address. |
| ESP/OTA | User-entered HTTP/HTTPS device URL, direct USB serial flashing/provisioning, and explicit-host OTA calls. HTTP Basic credentials optional for OTA. No LAN-wide automatic ESP discovery. USB enumeration and Wi-Fi SSID scanning are distinct. Native phone MQTT test is connect/disconnect only; discovery publishing is delegated to the device. |
| MQTT | Paho 1.2.5 sends username/password to user-chosen broker; temporary client ID; in-memory persistence/clean session. Port 8883 or explicit tls option uses TLS with hostname verification, otherwise TCP. |
| Radio directory | Packaged app.js calls country-list loading during startup. Radio Browser receives HTTPS requests to all.api.radio-browser.info/json/countries and, when chosen/restored, /stations/bycountry/{country}. Receives IP/HTTP metadata and country preference. No GPS inference code in ELMA. Not an entirely offline app. |
| Artwork / device web pages | Shared source and packaged bundle contain Apple iTunes search for title/artist/album/filename during a device-storage media preview, plus artwork requests. Android Designer hides storage views and lacks a live device filesystem, so this is not a startup artwork upload. Device mode loads pages from the chosen device, whose behavior depends on firmware. Do not label the dormant packaged function as unconditional Android collection. |
| Browser CDN references | Shared browser USB flasher contains UNPKG/jsDelivr dynamic imports behind Web Serial support. Android Build uses bundled esptool-js; packaged page CSP only permits same-origin scripts. Do not declare automatic CDN downloads from merely finding strings. |
| ELMA servers | No central ELMA telemetry, analytics, account or cloud configuration endpoint found. User-selected PC/broker/device endpoints and third-party services still transmit data off the phone. |
| Local storage | MainActivity preferences store full config and device URL; elma-build preferences store pairing credentials; elma-purchase stores verified receipt/signature. WebView local/session storage holds preferences, radio selections and applicable caches. No app-layer encryption for preferences. Backup disabled and extraction rules exclude cloud/device transfer. |
| Deletion | Android clear-storage/uninstall removes app storage. Exports, PC artifacts, ESP config, broker/Google records require separate handling. No ELMA account exists. Existing in-app policy already directs emailed support deletion requests to elik745i@gmail.com. |
| Other libraries | usb-serial-for-android 3.10.0/esptool-js communicate with selected hardware; fflate/@noble/hashes process archives/checksums. No added developer telemetry endpoint found. |

## Play Console worksheet

Use the public HTML policy as the Privacy Policy URL. This worksheet is not an automatically submitted form. It separates verified app behavior from provider processing that cannot be settled by source alone.

Google's [Data Safety guidance](https://support.google.com/googleplay/android-developer/answer/10787469?hl=en) distinguishes local processing, off-device collection and sharing exceptions. Apply it to the actual distributed build and all active versions; user-initiated sharing exceptions do not automatically exempt collection. Do not answer “no data collected” solely because there is no ELMA backend.

| Declaration / data category | How to answer or assess for this source |
| --- | --- |
| All data encrypted in transit | **No universal encryption claim.** HTTP device/OTA credentials and ordinary TCP MQTT exist. Do not select “Yes, all data is encrypted in transit” for these flows. |
| Account creation | **No ELMA account creation.** Google Play account use does not add an ELMA account. Do not claim an implemented in-app ELMA account-deletion flow. |
| Data deletion | Local clear-storage/uninstall and separate export removal are documented. Support page accepts privacy/deletion requests for information sent to the developer; this does not delete data held by Google, brokers, PCs or ESPs. Match any deletion-form answer to this scope. |
| Precise location | No phone coordinates collected/transmitted by app-owned code; Wi-Fi permissions alone do not support declaring GPS tracking. Raw SSID scan stays local. Check separate SDK processing before finalizing location categories. |
| User IDs / other personal info | Broker usernames and credentials, configurable identities and authentication settings may be transmitted on chosen connection/build/provision operations. Map actual fields to the relevant category; app functionality/authentication, optional feature use. No ELMA registration profile. |
| Other user-generated content / files and docs | Full configuration and imported bundle content can be sent to chosen PC/ESP; explicit export creates a separate copy. Device-page uploads may transfer selected files. Assess actual file types and recipients. Purpose: app functionality. Optional feature use. Local-only import/storage is not itself off-device collection. |
| Device or other IDs | Temporary MQTT ID travels to broker; SDK identifiers require assessment below. USB IDs read only on-device are not by themselves off-device collection. Do not invent a persistent phone ID. |
| In-app search history / other actions | Country filter is sent to Radio Browser when selected/restored; assess that preference/query under the relevant app-activity category. Startup countries request has no user search term but exposes IP and HTTP metadata. App functionality. Country selection is optional; country-list request has no dedicated opt-out toggle. |
| Approximate location from IP | IP necessarily reaches Radio Browser/other network recipients. Whether a recipient derives location is not established by Android source. Do not infer that a selected radio country is physical location. Verify provider use before assigning IP to a location category. |
| Purchase history | Queries/restoration are active even though checkout is blocked. Valid receipt caching/acknowledgement is conditional on a verifiable existing purchase. Assess off-device purchase-token/transaction handling for the configured release and Play SDK. App functionality and fraud prevention. Do not claim card-number collection by ELMA. |
| AdMob categories | If ads are enabled, review approximate location, device/other IDs, app interactions and diagnostics for both collection and sharing, with advertising, analytics and fraud-prevention purposes. Current app-owned ad request path is disabled. Do not represent future live ads as current, or assume disabled calls remove every SDK component. |
| UMP and SDK diagnostics | SDK/provider network behavior, retention, optionality and sharing need confirmation for the exact release. UMP menu call and Billing client are present; a no-traffic claim has not been verified. |
| Audio / photos / media / metadata | No broad photo access or recording. Device pages can upload selected files and perform media artwork searches. If the shipped user journey exposes these, classify transferred media and filename/track search metadata by actual content and destination. Dormant offline code is not proof it runs. |
| Support | Email address/message/attachments are voluntarily sent through external email, not an ELMA in-app contact form. Assess applicable user-initiated transfer rules; do not claim automatic address-book or email-inbox collection. Public GitHub posts are voluntary and public. |

Google's [Mobile Ads disclosure for 25.4.0](https://developers.google.com/admob/android/privacy/play-data-disclosure) documents IP-derived location, interactions, diagnostics and identifiers. This describes the SDK, not a measured network trace of the disabled app. Check [Google's UMP documentation](https://developers.google.com/admob/android/privacy) when configuring consent. Provider retention and deletion cannot be inferred from the app code.

## Remaining release checks

- Confirm actual Play-distributed artifact, SDK traffic/retention and whether any other active versions enable ads. The checked source alone does not support final “no collection” or “no sharing” assurances.
- Update the Android app's existing bundled privacy text to include Radio Browser and align with this policy in a separate authorized change. Neither existing repository was modified for this website task.
- Enter the public policy/support URLs in Play Console. Review data categories, purposes, required/optional status and sharing exceptions against the release configuration. Do not mark the app independently security-reviewed; this source audit is not that certification.
- Revisit the policy and declarations before enabling live monetization or adding services. This site does not itself resolve an Android release's security or disclosure gaps.

## Website privacy

No site scripts, form, login, analytics, external fonts or tracking pixels. All styles/logo assets are local. GitHub Pages [logs visitor IP addresses for security](https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages#data-collection). This website behavior is disclosed separately from Android behavior.
