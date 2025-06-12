# Setting Up Your App in Google Play Console

This guide will help you set up your app in the Google Play Console, ensuring all requirements are met for testing and release.

## Prerequisites

### Essential Information
- Application ID (permanent after creation), eg. `com.company.app`
- App name (maximum 50 characters, editable later)
- Content ratings questionnaire email (eg. `hello@company.com`)
- App type and category ([Documentation](https://support.google.com/googleplay/android-developer/answer/9859673))
- Appropriate labels
- Age rating classification (18+ or made for kids)

### Required Assets [(Documentation)](https://support.google.com/googleplay/android-developer/answer/9866151)

The design team should provide the following assets:

- **App Icon**
    - Dimensions: 512x512 pixels
    - Format: PNG with 32-bit alpha channel
    - Maximum size: 1024KB
    - [Design specifications](https://developer.android.com/distribute/google-play/resources/icon-design-specifications)

- **App Description**
    - Short description: 80 characters maximum
    - Long description: 4000 characters maximum
    - Required for all supported languages

- **Screenshots**
    - Quantity: 2-8 screenshots
    - Format: JPEG or 24-bit PNG (no alpha channel)
    - Maximum size: 8 MB
    - Aspect ratio: 16:9 or 9:16

- **Feature Graphic**
    - Dimensions: 1024x500 pixels
    - Format: JPEG or 24-bit PNG (no alpha channel)

### Required Declarations
- Advertising ID
- Data safety
- Health apps
- Financial features
- Additional relevant forms

!!! note "Please complete all required declarations as soon as possible. Some of them are required for uploading the app to the testing tracks."

## Key Management

You can use *Play App Signing* to have Google generate the key, or you can provide it to Google when you first upload the application. **Using 
Play App Signing by Google is recommended.** See [Play App Signing](https://support.google.com/googleplay/android-developer/answer/9842756).

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
   
    Store the following in **Futured Bitwarden** (Android Collection, accessible to the team):

    - Upload key (`.keystore` file)
    - Keystore password
    - Upload key password
    - Upload key alias

3. **Generate and Upload Signed Build**
    - Use Android Studio to generate a signed AAB using the upload key created in step 1.
    - Upload the signed build to a testing track in Google Play Console
    - If Play App Signing is enabled, Google will automatically generate the App Signing Key
    - After upload, you can locate the SHA-1 fingerprint of your App Signing Key in the Google Play Console under App Signing settings.

!!! info "About Fingerprints"
    - Do not forget to [add the SHA-1](../project_setup/30_firebase.md#sha-certificate-fingerprints) of the upload key to Firebase Console.
    - Ensure all third-party SDKs (Google Maps, Facebook login, etc.) are configured with the correct signing key information.