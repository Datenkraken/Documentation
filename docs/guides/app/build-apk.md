# How to build the App as an APK

- Open the project in Android Studio
- Go to Build -> Generate Signed APK -> APK (Android App Bundle works similarly)
- Click on "Create new ..."
- Choose a location to store the key, and a password to secure the key store
- Choose an alias (name for the key) and a password for the key (choose a different password than before)
- (optional) Set the validity year of the key (25 years or more are recommended)
- Fill in the Certificate (at least one field has to be filled)
- Click on next
- Choose a destination for the apk
- Choose the build type for the apk
- Choose the Signature versions (both possible)
    - The different schemes are shown here: [https://source.android.com/security/apksigning](https://source.android.com/security/apksigning)
- Click on finish
    - -> Android Studio should now build the apk

For more details visit: [https://developer.android.com/studio/publish/app-signing](https://developer.android.com/studio/publish/app-signing)