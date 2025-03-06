# Customer's account

To establish our CI flow and be able to deliver builds to customer's App Store Connect account and maintain app submission we need:
    
1. add `ops@futured.app` user to customer's App Store Connect account

    ??? note "Steps"
    
        - go to [App Store Connect](https://appstoreconnect.apple.com) and login
        - if your account is in more organizations make sure the correct one is selected
        - select `Users and Access`

            ![Step 1](Resources/ios_customer_account_1.png){ width="900" }

        - click `+` button

            ![Step 2](Resources/ios_customer_account_2.png){ width="900" }

        - fill a new user and click `Next` button
            - First Name: `Futured`
            - Last Name: `Operations`
            - Email: `ops@futured.app`
            - Roles: `App Manager`
            - Additional Resources: `Access to Certificates, Identifiers & Profiles.`
            !!! info "User's role"

                We need `App Manager` role to be able to maintain app submission. For more information about App Store Connect roles see [documentation](https://developer.apple.com/help/app-store-connect/reference/role-permissions/).
                
                We also need access to Certificates, Identifiers & Profiles to be able to properly setup out CI flow via Fastlane. For more information about Fastlane see [documentation](https://docs.fastlane.tools/).

            ![Step 3](Resources/ios_customer_account_3.png){ width="500" }

        - select desired app (if created) or keep selection blank and click `Invite` button

            === "The app is created already"

                ![Step 4a](Resources/ios_customer_account_4a.png){ width="500" }

            === "The app is not created yet"

                ![Step 4b](Resources/ios_customer_account_4b.png){ width="500" }
        
        - accept the invitation, you can find credentials in Futured's Bitwarden account (if you don't see it there please contact some senior developer)

2. create App Store Connect API key

    ??? note "Steps"
    
        - go to [App Store Connect](https://appstoreconnect.apple.com) and login
        - if your account is in more organizations make sure the correct one is selected
        - select `Users and Access`

            ![Step 1](Resources/ios_customer_account_1.png){ width="900" }
            
        - select:
        
            ![Step 5](Resources/ios_customer_account_5.png){ width="900" }
        
            1. `Integrations`
            2. `App Store Connect API`
            3. `Team Keys`
            4. copy `Issuer ID` and save it to company's Bitwarden (see for example `Futured App Store Connect API Key` entry, don't forget to select `iOS` and `Operations` collections)
            5. click `+` button, fill key name (there's no naming convention), `App Manager` access role and click `Generate` button
            
                ![Step 6](Resources/ios_customer_account_6.png){ width="500" }
                
                !!! info "Access"

                    We need `App Manager` access to be able to maintain app submission. For more information about App Store Connect roles see [documentation](https://developer.apple.com/help/app-store-connect/reference/role-permissions/).
                
                    We also need access to Certificates, Identifiers & Profiles to be able to properly setup out CI flow via Fastlane. For more information about Fastlane see [documentation](https://docs.fastlane.tools/).
            
            6. new key will appear in the list, please copy `KEY ID` and save it to company's Bitwarden (to the same entry as in step `d.`)
            7. key is downloadable only once, please download it and save it to company's Bitwarden (to the same entry as in step `d.`)
            
                ![Step 7](Resources/ios_customer_account_7.png){ width="500" }
