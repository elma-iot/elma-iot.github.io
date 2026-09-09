# ELMA IoT official website

Public static website for ELMA IoT, maintained by Elnur Mehdiyev.

- Home: https://elma-iot.github.io/
- Android Privacy Policy: https://elma-iot.github.io/privacy-policy/
- Support: https://elma-iot.github.io/support/

## Publishing

GitHub Pages publishes the root directory of the `main` branch. `.nojekyll` selects plain static files. No build system, JavaScript framework, remote font, analytics script, account system or secret is required.

Edit the HTML/CSS, check relative links, then push to `main`. GitHub Pages handles deployment and HTTPS.

For a local preview, run `python -m http.server 8765` in this directory and open http://localhost:8765/. All styles and images are local assets.

## Privacy review

The policy was prepared on 9 September 2026 from Android version 1.0.0, application ID `com.elma.iot.flasher`, at source commit `d4c2760253ac25fe52826f4ad333c49ceb252d9f`. The local Android checkout matched its GitHub `main` commit and was clean. The review included Java, Gradle, source and merged release manifests, packaged WebView assets and their shared source. It is a source review, not a runtime network capture or a Google Play approval.

See [ANDROID-PRIVACY-REVIEW.md](ANDROID-PRIVACY-REVIEW.md) for findings and a Data Safety worksheet. Review both documents whenever the Android release, SDK configuration or actual processing changes. The policy conditionally describes planned enabled advertising/checkout while preserving a dated finding that both remain disabled. The worksheet separates current behavior from production assumptions; final configuration and provider processing must still be verified.

The Android and firmware repositories are separate and were not changed by this website publication. The existing Android in-app privacy page is still its previous bundled copy; align that copy with this policy in a separately authorized Android update before submitting the release to Google Play. In Play Console, enter the Privacy Policy and Support URLs above.
