# CI/CD

You may be asked to provide *match password* when calling Fastlane lanes, please use `Fastlane Match password` entry from Futured's Bitwarden.

## Fastlane configuring 

For importing Futured's Fastlane flow to your project, add `fastlane/Fastfile` to project's folder and insert this code:

```ruby

ENV['APP_NAME'] = 'AppName'
ENV['APP_SCHEME'] = 'AppName'
ENV['APP_IDENTIFIER'] = 'app.futured.app_name'
# If you use any app's extension (notification, widget, etc.), uncomment next line and fill all the extension names (comma separated):
#ENV['APP_EXTENSIONS'] = 'extension1,extension2'

# If you want beta builds with dark badge on app icons, uncomment next line:
#ENV['BADGE_DARK'] = 'true'

# In case the app is released under partner's developer account setup following variables:
#ENV['APP_IDENTIFIER_CUSTOMER'] = 'cz.our.futuristic.partner'
#ENV['TEAM_ID_CUSTOMER'] = 'AAAAAAAAAA'
# Use these steps to get ASC ID: https://stackoverflow.com/a/46415415/3887546 
#ENV['ASC_TEAM_ID_CUSTOMER'] = '123456'
#ENV['CUSTOMERS_BRANCH'] = 'partner'

import_from_git(url: 'git@github.com:futuredapp/fastlane.git')

```
    
## Renew provisioning profile

![Step 1](Resources/ios_ci_cd_1.png){ width="900" }

In the case of an expired provisioning profile (see the attached CI error screenshot 👆) or when adding capabilities, it is necessary to take the following steps:

- delete provisioning profile in the Apple Developer portal
- for renew expired profile run:
    ```bash
    bundle exec fastlane update_provisioning # for debug/beta configuration
    ```
    ```bash
    bundle exec fastlane update_release_provisioning # for release configuration
    ```

- for renew profile after adding capabilities run:
    ```bash
    MATCH_FORCE=true bundle exec fastlane update_provisioning # for debug/beta configuration
    ```
    ```bash
    MATCH_FORCE=true bundle exec fastlane update_release_provisioning # for release configuration
    ```

## Renew certificate

![Step 2](Resources/ios_ci_cd_2.png){ width="900" }

In the case of an expired certificate (see the attached CI error screenshot 👆), it is necessary to take the following steps:

- delete provisioning profiles related to the given certificate in the Apple Developer portal
    - there's no need to delete the certificate itself, it will be removed automatically

- remove the corresponding development or distribution certificate (both the `.cer` and `.p12` files) from the `futured/apple-certificates` repository

    ![Step 3](Resources/ios_ci_cd_3.png){ width="500" }

    - for Futured account, find it in the default branch
    - for client's account, find it in the appropriate branch
    - open a PR pointing to the right branch, you can merge it immediately

- for renew expired certificate and corresponding profiles run:
    ```bash
    bundle exec fastlane update_provisioning # for debug/beta configuration
    ```
    ```bash
    bundle exec fastlane update_release_provisioning # for release configuration
    ```
    
##  Adding existing certificate to the `futured/apple-certificates` repository

It might be necessary if the client has reached the maximum number of generated certificates on their Apple Developer account.

Prerequisites:

- `.cer` file
- `.p12` file (SSL certificate with its private key within a single encrypted file)
    - it's possible the file is password protected, you need to know the password and remove it
        - import it into your keychain
        - export it from keychain without a password
        

Clients or previous suppliers should be able to provide this. Note that the certificate can be downloaded from the Apple Developer account, but only the `.cer` file. If the `.p12` file is not available, a new certificate must be created and stored securely with its private key.

**After you obtain necessary files store them to Futured's Bitwarden.**
    
Steps:

1. Create a project branch in `futured/apple-certificates` repository
2. Import the certificate with following script:
    ```bash
    fastlane match import \
    —ops@futured.app \
    --git_url https://github.com/futuredapp/apple-certificates \
    --app_identifier app.futured.xyz \ # replace app_identifier
    --team_id XYZXYZXYZX \ # replace team_id
    --type appstore \
    --git_branch yourbranch \ # replace git_branch
    --clone_branch_directly true
    ```

    1. you will be sequentially asked to provide the path to:
        - *.cer* file
        - *.p12* file
        - *provisioning profile* - `.mobileprovisioning` or `.provisioningprofile` file
            - it's optional, ignore it, you will create a new provisioning profile later
        - *match password*, please use `Fastlane Match password` entry from Futured's Bitwarden
        - *username*, use `ops@futured.app`

3. Generate a new release provisioning profile:
    - see [Renew provisioning profile](#renew-provisioning-profile) on this page
    
The import process should look as follows:
    
![Step 4](Resources/ios_ci_cd_4.png){ width="900" }
