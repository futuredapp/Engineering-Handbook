## Code style

Code style file is located [here](https://github.com/thefuntasty/android-treasure/blob/master/codestyle/funtasty_code_style.xml).

### How To install
1. Download file `funstasty_code_style.xml`
2. In Android Studio: Settings (`CMD + ,`) > Editor > Code Style > Scheme
3. Press ⚙️ icon, select `Import scheme` and pick downloaded file
4. Press `Apply`

## Project structure

Every project should respect following file and package hierarchy:

```
project-android
|-- app
|   |-- src/main/
|   |   |-- kotlin/com/example
|   |   |   |-- data  
|   |   |   |   |-- database
|   |   |   |   |   `-- AppDatabase.kt
|   |   |   |   |-- model
|   |   |   |   |   `-- User.kt
|   |   |   |   |-- remote
|   |   |   |   |   `-- RetrofitService.kt
|   |   |   |   `-- store
|   |   |   |       `-- UserStore.kt
|   |   |   |-- domain
|   |   |   |   `-- feature/FeatureInteractor.kt
|   |   |   |-- injection  
|   |   |   |   |-- module
|   |   |   |   |   |-- ActivityBuilderModule.kt
|   |   |   |   |   `-- ApplicationModule.kt
|   |   |   |   `-- component
|   |   |   |       `-- ApplicationComponent.kt
|   |   |   |-- tools/constant/Constants.kt
|   |   |   |-- ui 
|   |   |   |   |-- base/BaseActivity.kt
|   |   |   |   `-- main
|   |   |   |       |-- MainActivity.kt
|   |   |   |       |-- MainActivityModule.kt
|   |   |   |       |-- MainView.kt
|   |   |   |       |-- MainViewModel.kt
|   |   |   |       |-- MainViewModelFactory.kt
|   |   |   |       `-- MainViewState.kt
|   |   |   `-- App.kt 
|   |   `-- res/layout/activity_main.xml 
|-- keystore
|   |-- debug.jks
|   |-- client.jks
|   `-- release.jks
|-- .gitinore
|-- .editorconfig
|-- Dangerfile
|-- detekt.yml
```
