1. Install the official [EversenseApp](https://play.google.com/store/apps/details?id=com.senseonics.gen12androidapp) from the Playstore

2. Extract the APK of this App (e.g. with [MyAppSharer](https://play.google.com/store/apps/details?id=com.yschi.MyAppSharer))

3. Upload this APK (com.senseonics.gen12androidapp) to your PC

4. Decompile the APK to .smali code (e.g. with [Apktool](https://ibotpeaches.github.io/Apktool/install))
```
./apktool.sh d -o eversense -p /home/tanja/Android/Sdk com.senseonics.gen12androidapp_1.0.406.apk
```

5. In the decompiled app, navigate to `com.senseonics.gen12androidapp\smali\com\senseonics\db`

6. Copy/duplicate `ConnectedTransmitterContentProvider.smali` and name the new file `GlucoseContentProvider.smali` (see the file [GlucoseContentProvider.smali](https://github.com/BernhardRo/Esel/blob/Eversense_mod/mod/GlucoseContentProvider.smali) in this repo for references)

7. Edit `GlucoseContentProvider.smali` and
  * rename every instance of `ConnectedTransmitterContentProvider` to `GlucoseContentProvider`
  * rename `connectedTransmitters` to `glucosereadings` (in the SQL statements)
    - except of line 29: for security reasons, rename the string in `performDelete` to something like `deletenotsupported`

8. Modify `com.senseonics.gen12androidapp\AndroidManifest.xml`: Search for
```
<provider android:authorities="com.senseonics.gen12androidapp.transmitter"...
```
and replace that line with the following two lines:
```
<provider android:authorities="com.senseonics.gen12androidapp.transmitter" android:grantUriPermissions="true" android:exported="true" android:name="com.senseonics.db.ConnectedTransmitterContentProvider"/>
<provider android:authorities="com.senseonics.gen12androidapp.glucose" android:grantUriPermissions="true" android:exported="true" android:name="com.senseonics.db.GlucoseContentProvider"/>
```
(see the file [AndroidManifest.xml](https://github.com/BernhardRo/Esel/blob/Eversense_mod/mod/AndroidManifest.xml) in this repo for references)

9. Compile the code according the documentation of your tool you have used to decompile it
With apktool, this would be
```
./apktool.sh b -p /home/tanja/Android/Sdk/ -o com.senseonics.gen12androidapp_1.0.406_patched.apk eversense```

10. Sign the compiled APK (See the Android [Documentation](https://developer.android.com/studio/publish/app-signing#signing-manually) for help)

  You need to have an existing keystore and key for this step. You can re-use the keystore you use for generating the signed AAPS.apk)
```
jarsigner -keystore <keystore> -storepass <passwordKeystore> -keypass <passwordKey> com.senseonics.gen12androidapp_1.0.406_patched.apk <keyName>
```

11. Uninstall the original Eversense App on your phone (Warning: the local history of your CGM readings in your Eversense App will get lost) and install your new APK. The new App will behave just like the original one - except of the difference that the CGM reading can be accessed from other Apps e.g. by ESEL

  Tipp: If you signed the apk with the same key that the current apk is signed with, you can install the new version as update on the existing and don't need to uninstall! 
