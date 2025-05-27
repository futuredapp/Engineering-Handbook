# Setup app in Google Play Console

This page describes **setting up app in the Google Play Console**.

## Requirements to create application
- [ ] Package name. Cannot be changed later.
- [ ] App name (max 50 characters). Can be changed later.
- Assets - [docs](https://support.google.com/googleplay/android-developer/answer/9866151)
    - [ ] App icon: 512x512 px, PNG, 32-bit with alpha, max size: 1024KB. [Design specifications](https://developer.android.com/distribute/google-play/resources/icon-design-specifications)
    - [ ] Description: short (80 characters) + long (max 4000 characters) for all supported languages.
    - [ ] Screenshots for phones or tablets: 2-8 screenshots, JPEG or 24-bit PNG (no alpha), max. 8 MB, ratio: 16:9 or 9:16.
    - [ ] Feature graphic: JPEG or 24-bit PNG (no alpha), 1024x500 px.
- [ ] E-mail for content ratings questionnaire.
- [ ] App type and category. [Docs](https://support.google.com/googleplay/android-developer/answer/9859673)
- [ ] Labels.
- [ ] Declarations: *Advertising ID*, *Data safety*, *Health apps*, *Financial features*, ... there are multiple forms to fill out.
- [ ] Age rating: 18+ / made for kids.
- [ ] To create `upload key certificate` for sign app to upload to Google Play Console see section: *Upload and App Signing Key* below.
- [ ] Setup MD5 / SHA-1 / SHA-256 fingerprints for `upload key certificate` in Google Play Console.
- [ ] Link to privacy policy.
- [ ] Set emails to Internal / Closed / Open testing. [Docs](https://support.google.com/googleplay/android-developer/answer/9845334)
- [ ] Set countries for release.

!!! warning
    - Requirement for *Closed* testing for personal account: at least 12 testers for the 14 days continuously must be opted-in to your closed test when you apply for production access. [Docs](https://support.google.com/googleplay/android-developer/answer/14151465)


## Upload and App Signing Key
- The *Upload Key* is used by the developer to sign the app bundle before uploading it to the Play Console
- The *App Signing Key* is used by Google to re-sign the app bundle after upload, ensuring secure delivery to users.

The *Upload Key* is managed by the developer, while Google manages the *App Signing Key*.

### Upload key
Signing APK/AAB to upload to Google Play Console.

- [ ] Generate upload key with client's information and store the upload key to **Futured Bitwarden**.
- [ ] **Futured Bitwarden** should contains:
    - [ ] Upload key. `JKS` file.
    - [ ] Upload key password.
    - [ ] Alias name.
    - [ ] Alias password.

!!! tip
    Do not use generic names for the upload key, such as *upload*, *android*, etc.

### App Signing key
You can use *Play App Signing* to generate key by Google or you can provide it to Google when you first upload the application. **Usage Play App signing by Google is recommended.** See [Play App Signing](https://support.google.com/googleplay/android-developer/answer/9842756)

- [ ] Add SHA-1 to production version of app to `Firebase / settings / project settings / tab general`

!!! Info "Do not remember"
    3rd party SDKs like Google Maps, Facebook login, etc.
