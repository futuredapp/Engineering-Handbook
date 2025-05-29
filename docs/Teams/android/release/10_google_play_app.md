# Setup app in Google Play Console

This page describes **how to set up an app in the Google Play Console**.

## Requirements to create an application
- [ ] Package name. Cannot be changed later.
- [ ] App name (max 50 characters). Can be changed later.
- Assets - [docs](https://support.google.com/googleplay/android-developer/answer/9866151).
    - [ ] App icon: 512x512 px, PNG, 32-bit with alpha, max size: 1024KB. See [Design specifications](https://developer.android.com/distribute/google-play/resources/icon-design-specifications).
    - [ ] Description: short (80 characters) and long (max 4000 characters) for all supported languages.
    - [ ] Screenshots for phones or tablets: 2-8 screenshots, JPEG or 24-bit PNG (no alpha), max. 8 MB, ratio: 16:9 or 9:16.
    - [ ] Feature graphic: JPEG or 24-bit PNG (no alpha), 1024x500 px.
- [ ] E-mail for the content ratings questionnaire.
- [ ] App type and category. See [Docs](https://support.google.com/googleplay/android-developer/answer/9859673).
- [ ] Labels.
- [ ] Declarations: *Advertising ID*, *Data safety*, *Health apps*, *Financial features*, ... there are multiple forms to fill out.
- [ ] Age rating: 18+ / made for kids.
- [ ] To create an `upload key certificate` for signing the app to upload to the Google Play Console, see section: *Upload and App Signing Key* below.
- [ ] Set up MD5 / SHA-1 / SHA-256 fingerprints for `upload key certificate` in the Google Play Console.
- [ ] Link to privacy policy.
- [ ] Set emails for Internal / Closed / Open testing. See [Docs](https://support.google.com/googleplay/android-developer/answer/9845334).
- [ ] Set countries for release.

!!! warning
    - Requirement for *Closed* testing for personal accounts: at least 12 testers must be opted-in to your closed test continuously for 14 days when you apply for production access. See [Docs](https://support.google.com/googleplay/android-developer/answer/14151465).


## Upload and App Signing Key
- The *Upload Key* is used by the developer to sign the APK/AAB before uploading it to the Play Console.
- The *App Signing Key* is used by Google to re-sign the APK/AAB after upload, ensuring secure delivery to users.

The *Upload Key* is managed by the developer, while Google manages the *App Signing Key*.

### Upload key

1. **Generate the upload key** with the client's information. Generating the upload key can be done in several ways:

    - By Android Studio - **recommended**:
        - Open the *Build* menu.
        - Select *Generate Signed Bundle / APK*.
        - Choose *Android App Bundle* or *APK* and click *Next*.
        - Select *Create new...* under the *Key store path* field.
        - Fill in the required fields and click *OK*.

    - or you can use the `keytool` command to generate the key:
         ```bash
          keytool -genkey -v -keystore your-upload-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias your-alias-name
         ```

    !!! tip
        Avoid using generic names for the upload key, such as *upload*, *android*, etc.

2. **Store the upload key** in **Futured Bitwarden**. It should contain:
    - [ ] Upload key (`.jks` file).
    - [ ] Upload key password.
    - [ ] Alias name.
    - [ ] Alias password.

### App Signing key
You can use *Play App Signing* to have Google generate the key, or you can provide it to Google when you first upload the application. **Using Play App Signing by Google is recommended.** See [Play App Signing](https://support.google.com/googleplay/android-developer/answer/9842756).

- [ ] Add the SHA-1 of the production version of the app to the Firebase Console.
    1. Go to *Google Play Console / Test and release / Setup / App signing / App signing key certificate* and copy `SHA-1 certificate fingerprint`.
    2. Paste it into the *Firebase / Settings / Project settings / General tab* in the `SHA certificate fingerprints` field.

!!! info "Important Note"
    Remember to configure 3rd party SDKs like Google Maps, Facebook login, etc., with the appropriate signing key information.
