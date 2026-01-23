# Setting Up Your App in Google Play Console

This guide will help you set up your app in the Google Play Console, ensuring all requirements are met for testing and release.

## Requirements to create an application
- Application ID (package name) - cannot be changed later
- App name (maximum 50 characters) - can be changed later
- Assets (see [documentation](https://support.google.com/googleplay/android-developer/answer/9866151)):
    - App icon: 512x512 px, PNG, 32-bit with alpha, maximum size: 1024KB (see [design specifications](https://developer.android.com/distribute/google-play/resources/icon-design-specifications))
    - Description: short (80 characters) and long (maximum 4000 characters) for all supported languages
    - Screenshots for phones or tablets: 2-8 screenshots, JPEG or 24-bit PNG (no alpha), maximum 8 MB, ratio: 16:9 or 9:16
    - Feature graphic: JPEG or 24-bit PNG (no alpha), 1024x500 px
- Email address for the content ratings questionnaire
- App type and category (see [documentation](https://support.google.com/googleplay/android-developer/answer/9859673))
- Labels
- Declarations: *Advertising ID*, *Data safety*, *Health apps*, *Financial features*, and other required forms
- Age rating: 18+ or made for kids
- Create an `upload key certificate` for signing the app (see [Key Management](#key-management) section below)
- Set up MD5, SHA-1, and SHA-256 fingerprints for the `upload key certificate` in the Google Play Console
- Privacy policy link
- Testing track emails for Internal, Closed, and Open testing (see [documentation](https://support.google.com/googleplay/android-developer/answer/9845334))
- Release countries

!!! note "Please complete all required declarations as soon as possible. Some of them are required for uploading the app to the testing tracks."

## Key Management

You can use *Play App Signing* to have Google generate the key, or you can provide it to Google when you first upload the application.
This guide uses [Play App Signing](https://support.google.com/googleplay/android-developer/answer/9842756).

### Understanding the Keys
- **Upload Key**: Used by developers to sign APK/AAB before Play Console upload.
- **App Signing Key**: Used by Google to re-sign APK/AAB after upload for secure delivery.

### Upload Key Setup

1. **Generate the Upload Key**

    **Option 1: Android Studio (Recommended)**

    1. Navigate to the *Build* menu
    2. Select *Generate Signed Bundle / APK*
    3. Choose between *Android App Bundle* or *APK*
    4. Click *Next*
    5. Select *Create new...* under *Key store path*
    6. Complete the form and click *OK*

    **Option 2: Command Line**
    ```bash
    keytool -genkey -v -keystore your-upload-key.keystore -keyalg RSA -keysize 2048 -validity 10000 -alias your-alias-name
    ```

    !!! tip "Naming Convention"
        Avoid generic names like "upload" or "android" for your key aliases. Using generic names will make it harder to inspect GitHub CI logs, as these properties are usually passed as secrets and will get masked.
        
        For example, if you use `upload` as the alias name, your logs can contain something like this: `Task :app:****CrashlyticsMappingFile SKIPPED`, etc. Using `android` can be even worse, as many logs contain the word `android` in them.

2. **Store the Upload Key**
   
    Store the upload key credentials inside our **Bitwarden**. Create, or use existing entry for mobile application inside `Android` Collection. Owner should be set to `Futured`.

    - Upload key (`.keystore` file)
    - Keystore password
    - Upload key password
    - Upload key alias

    Store the upload key `.keystore` file in repo alongside debug keystore. Make sure the release signing config in Android Gradle Plugin correctly references the keystore file.


3. **Generate and Upload Signed Build**
    - Use Android Studio to generate a signed AAB using the upload key created in step 1.
    - Manually upload the signed build to a testing track for the first time in Google Play Console
    - Google will automatically generate the App Signing Key
    - After upload, you can locate the SHA-1 fingerprint of your App Signing Key in the Google Play Console under App Signing settings.

!!! info "About Fingerprints"
    - Do not forget to [add the SHA-1](../project_setup/40_firebase.md#sha-certificate-fingerprints) of the **App Signing Key** key to Firebase Console.
    - Ensure all third-party SDKs (Google Maps, Facebook login, etc.) are configured with the correct signing key information.