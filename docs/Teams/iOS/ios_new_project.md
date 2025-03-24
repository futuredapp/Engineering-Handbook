# New project

1. Create a new GitHub repository

    ??? note "Steps"
        
        - open [iOS project template](https://github.com/futuredapp/iOS-project-template) and click `Use this template` -> `Create a new repository`

            ![Step 1](Resources/ios_new_project_1.png){ width="900" }
        
        - fill new repository info
        
            ![Step 2](Resources/ios_new_project_2.png){ width="500" }

            1. change owner to `futuredapp`
            2. preffered repo name is *[product-name]-[platform]*, where *[product-name]* includes only name of the product, without client name (like Skoda))
            3. change repo visibility to `private`
            4. click `Create repository` button
        
        - you will be redirected to the new repo in couple of seconds
        - in newly created repo:

            ![Step 3](Resources/ios_new_project_3.png){ width="900" }
            
            1. click `Settings` button
            2. edit default branch name -> rename it to `develop`
            
                ![Step 4](Resources/ios_new_project_4.png){ width="500" }

            3. disable `Wikis` (we do not use it)
            4. disable `Issues` (we do not use it)
            5. disable `Projects` (we do not use it)
            6. enable `Allow auto-merge`
            7. enable `Automatically delete head branches` (there is no need to delete branches manually after each PR merge)
            8. select `Rules` section

                1. select `Rulesets` subsection
                2. click `New ruleset` button
                3. click `New branch ruleset` option from dropdown
            
                    ![Step 5](Resources/ios_new_project_5.png){ width="900" }
                    
            9. set new branch ruleset
            
                ![Step 6](Resources/ios_new_project_6.png){ width="900" }
                
                1. fill `default branch protection` to `Ruleset Name`
                2. change `Enforcement status` to `Active`
                
                    ![Step 7](Resources/ios_new_project_7.png){ width="500" }
                    
                3. click `Include default branch` to add default branch as target branch 
                
                    ![Step 8a](Resources/ios_new_project_8a.png){ width="500" }
                    ![Step 8b](Resources/ios_new_project_8b.png){ width="500" }
                    
                4. enable `Require a pull request before merging`
                
                    ![Step 9](Resources/ios_new_project_9.png){ width="500" }
                
                    1. set `Required approvals` to 1
                    2. enable `Dismiss stale pull request approvals when new commits are pushed`
                    
                5. keep `Require status checks to pass` unchecked for the moment (we'll set it up later)
                
                    - don't forget to add the `test` action rule after opening your first PR (actions will appear in the list after their first run)
                    
                    ![Step 10](Resources/ios_new_project_10.png){ width="500" }
                    
                6. click `Create` button, new ruleset appears in the list
                
                    ![Step 11](Resources/ios_new_project_11.png){ width="500" }
                    
3. Configure Ruby environment and Fastlane

    ??? note "Steps"
    
        - if you haven't setup ruby on your machine yet
        
            - follow manual steps of [this](https://www.moncefbelyamani.com/how-to-install-xcode-homebrew-git-rvm-ruby-on-mac/) tutorial
            - install bundler from Terminal with `gem install bundler`
            
        - in Terminal, switch to repo root folder
        - call `bundle install` to install gems
        - edit `fastlane/Fastfile` to specify environment variables
        - **TODO:**
        
            - add link to fastfile docs when ready
            - for now contact Kuba for help (if needed)

2. Create a new Xcode project

    ??? note "Steps"

        - clone your project's repo
        - create a new branch `feature/PROJ-1-Setup-project` where everything will be set up in next steps
        
            - PROJ is your project Jira identificator
            - 1 is Jira task number (if you don't have Jira yet ask your PM)

        - install `FuturedArchitecture` project template (if you haven’t done so yet)
        
            - paste the following script to Terminal and press enter
            ```bash
            bash << 'EOF'
            #!/bin/bash

            echo "Cloning repository..."
            git clone --depth=1 git@github.com:futuredapp/FuturedKit.git

            echo "Running make..."
            (cd FuturedKit/Templates && make)

            echo "Cleaning up..."
            rm -rf FuturedKit

            echo "Done!"
            EOF
            ```
        
        - open Xcode (or close and open again for refresh templates) and select`File` -> `New` -> `Project`
        
            ![Step 1](Resources/ios_new_project_12.png){ width="500" }
            
        - select `iOS` tab and `SwiftUI App` from `FuturedArchitecture` and click `Next` button
        
            ![Step 2](Resources/ios_new_project_13.png){ width="500" }
            
        - fill the project info and click `Next` button

            - write product name in CamelCased style
            - organization identifier is `app.futured`
            - leave *Include Tests* checked (CI requires tests run)
            
            ![Step 3](Resources/ios_new_project_14.png){ width="500" }
            
        - choose your repo folder and click `Create` button

            - Xcode project will open
            - close it and check the project's folder
            - move content of `{ProjectName}` folder to the repo root (you need to rename it first to be able to move the content)
            - delete `{ProjectName}` folder
            - open Xcode project again
            
            ![Step 4](Resources/ios_new_project_15.png){ width="500" }

        - in Xcode:
        
            - change bundle identifier to kebab-cased style `app.futured.kebab-case-app-name`

                1. select `root`
                2. select main project target
                3. select `Signing & Capabilities` tab
                4. change bundle id for all configurations
                
                ![Step 5](Resources/ios_new_project_16.png){ width="900" }
                
            - setup Futured's architecture
            
                - add `FuturedKit` package dependency - [https://github.com/futuredapp/FuturedKit.git](https://github.com/futuredapp/FuturedKit.git)
                
                    ![Step 6](Resources/ios_new_project_17.png){ width="500" }
                                    
                - add `FuturedArchitecture` to main project target
                - optionally add `FuturedHelpers` to main project target if needed - see [docs](https://github.com/futuredapp/FuturedKit?tab=readme-ov-file#futuredhelpers)
                
                    ![Step 7](Resources/ios_new_project_18.png){ width="500" }
                
                - go to the `{project_name}App.swift` file and remove error at line 10
                
                    ```swift
                    #error("Add https://github.com/futuredapp/FuturedKit.git to the project!")
                    ```
                
                    ![Step 8](Resources/ios_new_project_19.png){ width="900" }
                    
                - select `Issue navigator`, click the `Macro target ...` warning and then click `Trust & Enable` button
                
                    ![Step 9](Resources/ios_new_project_20.png){ width="900" }
                
            - add [SwiftLint](https://github.com/realm/SwiftLint)
            
                - if you don't have installed SwiftLint locally call `brew install swiftlint` from Terminal
                - go to `Build Phases` tab
                - click `+` button
                - click `New Run Script Phase`
                - rename phase to `SwiftLint` and move it before `Compile sources` phase
                - paste script bellow

                ```bash
                if test -d "/opt/homebrew/bin/"; then
                  PATH="/opt/homebrew/bin/:${PATH}"
                fi

                export PATH

                if which swiftlint >/dev/null; then
                    swiftlint --fix # remove `swiftlint --fix` if you want to fix linting issues manually
                    swiftlint
                else
                    echo "error: SwiftLint not installed, run: brew install swiftlint"
                fi
                ```
                
                ![Step 10](Resources/ios_new_project_21.png){ width="900" }
                ![Step 11](Resources/ios_new_project_22.png){ width="900" }
                
            - configure the project for Continuous Deployment

                - set proper values in `Versioning` part of `Build Settings`
                
                    - select all targets and remove `Marketing Version` value 
                    - select project and fill `1.0.0` as `Marketing Version`
                    
                    ![Step 12](Resources/ios_new_project_23.png){ width="900" }
                    ![Step 13](Resources/ios_new_project_24.png){ width="900" }
                    
                - select appropriate certificates, provisioning profiles for all configurations in the Target's Signing & Capabilities tab
                
            - build the project

4. Open pull request with the project setup

    ??? note "Steps"

        - update `.github/CODEOWNERS` file
            
            - change `@futuredapp/ios` to `@{github_username}` of teammate/teammates (or person who will perform code reviews)

        - update project README and fill in all the strike-through points
        - commit everything and open PR
        - after CI checks the PR, status checks are now available to be added in Branch protections settings. Add them.

5. Enjoy!
