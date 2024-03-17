## Signing Flutter app for release to Google Play Store

For Mobile Devs, the first timers or those who have not hosted any app on Google Play Store this is your time to save some time in this tricky process.
Let's say you have finished developing your first flutter app and tested locally with your device and don't know the next step to make it available to users,
don't worry it easier than you think when you have someone to guide you otherwise it can a bit of bottleneck sometimes. 

Let's say you have your Google Play Console account ready if not you might want to create one here **https://play.google.com/console/signup** for $25 one time
fee.

Now let's come back, our focus is signing the app so that you can be to upload it or make it ready for Play Console.

## Getting Started

To publish your app on the Play Store, you need a digital signature.  Use the following steps to sign your app.

1. Creating a keystore:
On `Mac/Linux`, use the following command
```bash
keytool -genkey -v -keystore .../sign_flutter_app/data/upload-keystore.jks -keyalg RSA \
        -keysize 2048 -validity 10000 -alias upload
```

Replace the 3 dots in the path provided  by your own filepath. If you want to store in home  directory, replace it with `~/` to `~/upload-keystore.jks`.

You can use any path to store the key as long you'll remember where you stored it. In this case we gonna store it in `/data/` folder inside the project for demonstration purposes.

Once you run the command , it will ask you for several information that:
    - Set keystore password, whereby in this demo I have used `sign_flutter_app` as the keystore password
    - Set your firs and last name, whereby in this demo gonna I have used `John` & `Doe` as first and last name respectively
    - Set name of your organization unit, where by in this demo I have used `Flutter Team`
    - Set name of your organization, where by in this demo I have used `Company Name`
    - Set name of your city or locality, where by in this demo I have used `CityName`.
    - Set name of your state or province, where by in this demo I have used `StateName`.
    - Set two-letter country code of your country, where by in this demo I have used `US`(United States).
    - Set email address, where by in this demo I have used `test@gmail.com`.


After running above command you can find generated keystore file at `.../sign_flutter_app/data/upload-keystore.jks`.


Note : You need JDK installed

Here is the whole process in the terminal for more clarity
```bash
keytool -genkey -v -keystore .../sign_flutter_app/data/upload-keystore.jks -keyalg RSA \
        -keysize 2048 -validity 10000 -alias upload
Enter keystore password:  
Re-enter new password: 
What is your first and last name?
  [Unknown]:  John Doe
What is the name of your organizational unit?
  [Unknown]:  Flutter Team
What is the name of your organization?
  [Unknown]:  Company Name
What is the name of your City or Locality?
  [Unknown]:  CityName
What is the name of your State or Province?
  [Unknown]:  StateName
What is the two-letter country code for this unit?
  [Unknown]:  US
Is CN=John Doe, OU=Flutter Team, O=Company Name, L=CityName, ST=StateName, C=US correct?
  [no]:  yes 

Generating 2,048 bit RSA key pair and self-signed certificate (SHA256withRSA) with a validity of 10,000 days
        for: CN=John Doe, OU=Flutter Team, O=Company Name, L=CityName, ST=StateName, C=US
[Storing .../sign_flutter_app/data/upload-keystore.jks]
```

2. Reference the keystore from the app
Create a filed named `key.properties` in `<app dir>/android` directory and add following content to it. Use the password from previous step and reference with the correct path where keystore was stored. In this case the path is `sign_flutter_app/android/key.properties`
```bash
storePassword=sign_flutter_app
keyPassword=sign_flutter_app
keyAlias=upload
storeFile=.../sign_flutter_app/data/upload-keystore.jks
```

Remember to replace the 3 dots with the actual path where you store  the keystore.

You should keep `key.properties` file private. Anyone can use it to sign apps that are built using your keystore.
The `keyPassword` should be the same as the `storePassword`.

3. Configure the `key.properties` signing in gradle
Configure gradle to use your upload key when building your app in release mode by editing the `[project]/android/app/build.gradle` file
 - Add the keystore information from your properties file before the `android block`:
 ```python
   def keystoreProperties = new Properties()
   def keystorePropertiesFile = rootProject.file('key.properties')
   if (keystorePropertiesFile.exists()) {
       keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
   }

   android {
         ...
   }
   ```

   -Find the `buildTypes` block:

   ```python
   buildTypes {
       release {
           // TODO: Add your own signing config for the release build.
           // Signing with the debug keys for now,
           // so `flutter run --release` works.
           signingConfig signingConfigs.debug
       }
   }
   ```

   And replace it with the following signing configuration previously set in key.properties

   ```python 
   signingConfigs {
       release {
           keyAlias keystoreProperties['keyAlias']
           keyPassword keystoreProperties['keyPassword']
           storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null
           storePassword keystoreProperties['storePassword']
       }
   }
   buildTypes {
       release {
           signingConfig signingConfigs.release
       }
   }
   ```

   - Now release builds of your app will now be signed automatically.

4. Clean your code 
   - you might need to run `flutter clean` after changing the gradle file. This prevents cached builds from affecting the signing process. Use this command:
   ```bash
   flutter clean
   flutter pub get
   ```

5. Finally here we come, now you can build `appbundle` to and upload it in Google Play Console and release the app. Use this command:
   ```bash
   flutter build appbundle
   ```
    - you'll find `.aab` file in this path `./build/app/outputs/bundle`

6. Upload to Play Store


# To contribute or want to make any change
- Fork this repository
- Create a branch for your feature (`git checkout -b my-feature`)
- Make your changes and commit them (`git commit -m ':heart: My Feature'`)
- Push to your origin branch (`git push origin my-feature`)
- Create a pull request

   
 








