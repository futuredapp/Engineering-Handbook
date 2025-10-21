# Setup for translations and multilanguage support

Our applications are mostly multilingual, and even if they're not, it is a general rule of thumb to maintain translations outside of the application project.
This allows us to share translations across multiple platforms. 

For this purpose, we usually use Google Sheets table combined with [Sheet happens](https://github.com/futuredapp/sheet-happens) gradle plugin.
It allows us to download translations from Google Spreadsheet in compatible strings.xml form into the Android projects. This solution is also compatible with [moko-resources](https://github.com/icerockdev/moko-resources) that is used as a multiplatform resources library on KMP projects, providing access to translations on all targeted platforms.

## Android native projects

For Android native projects based on our template, the setup is pretty straightforward. Just follow the instructions on [Sheet happens](https://github.com/futuredapp/sheet-happens) GitHub page.

## KMP projects 

#### 1. Add [sheet-happens](https://github.com/futuredapp/sheet-happens) gradle plugin

Add the Sheet happens plugin to your resource module's `build.gradle.kts` file:

```gradle
plugins {
    // ... other plugins
    alias(libs.plugins.sheet.happens)
}
```

#### 2. Configure plugin target folder

Configure the plugin to copy files to the proper moko-resources folder:

```gradle
sheetHappens {
    // ... other configuration
    resourcesLayout {
        resourcesDir.set(layout.projectDirectory.dir("src/commonMain/moko-resources"))
        splitResources.set(true)
    }
}
```

## Security Considerations

!!! warning "API Key Security"
    Always store sensitive API keys securely and never commit them to version control.

!!! note

    If you use Firebase, create the Google Sheets API key in your **Dev Firebase Google Cloud project** (not the production one).

For security reasons, extract the API key to a `local.properties` file that is not part of GitHub versioning:

```properties
# local.properties (not committed to Git)
sheetHappens.apiKey=your_api_key_here
```

Then reference it in your Gradle configuration:

```gradle
sheetHappens {
    // ... other configuration
    apiKey.set(localProperties.getProperty("sheetHappens.apiKey") ?: "")
}
```

