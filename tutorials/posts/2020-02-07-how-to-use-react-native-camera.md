---
date: 2020-02-07
title: 'How to use React Native Camera to read QR codes'
template: post
thumbnail: '../thumbnails/react.png'
slug: how-to-use-react-native-camera
categories:
  - React Native
tags:
  - react-native
---

![cover_image](https://blog.jscrambler.com/content/images/2020/02/jscrambler-blog-how-to-use-react-native-camera.jpg)

[React Native Camera](https://github.com/react-native-community/react-native-camera/) is the go-to component when creating React Native apps that require the functionality of using the device’s camera. Maintained by the [React Native community](https://react-native-community.github.io/react-native-camera/docs/rncamera), this module has support for:

- Videos
- Photographs
- Face Detection
- Text Recognition
- Barcode Scanning

It also helps your React Native app to communicate with the native operating system using the device's hardware by implementing some helper methods.

In this tutorial, let us build a simple QR code scanner app in React Native by implementing one of the functionalities this module supports, called Barcode scanning.

For more information on RNCamera, please refer to its official documentation [here](https://react-native-community.github.io/react-native-camera/docs/rncamera). The complete code for this tutorial is available at [this GitHub repo](https://github.com/amandeepmittal/qrCodeScanner).

## Installing Dependencies

To begin, let us generate a React Native project by using the command below from a terminal window:

```shell
npx react-native init qrCodeScannerApp

# navigate inside the directory once its generated
cd qrCodeScannerApp
```

Next, you have to install some dependencies to use the RNCamera module. If you are on the latest React Native version, that is, [a version above `60.x.x`](https://react-native-community.github.io/react-native-camera/docs/installation#mostly-automatic-install-with-autolinking-rn-060), run the following command from a terminal window.

```shell
yarn add react-native-camera
```

For iOS devices, you have to install the pods as shown below:

```shell
# after dependency installation
cd ios/

pod install

cd ..
```

For Android users, there is no extra installation requirement at this point.

## Setting Camera Permissions

To access the device's hardware camera, a set of permissions have to be added. For iOS, please open the file `ios/qrCodeScannerApp/Info.plist` and add the following permissions:

```plist
<!-- Required with iOS 10 and higher -->
<key>NSCameraUsageDescription</key>
<string>Your message to user when the camera is accessed for the first time</string>

<!-- Required with iOS 11 and higher: include this only if you are planning to use the camera roll -->
<key>NSPhotoLibraryAddUsageDescription</key>
<string>Your message to user when the photo library is accessed for the first time</string>

<!-- Include this only if you are planning to use the camera roll -->
<key>NSPhotoLibraryUsageDescription</key>
<string>Your message to user when the photo library is accessed for the first time</string>

<!-- Include this only if you are planning to use the microphone for video recording -->
<key>NSMicrophoneUsageDescription</key>
<string>Your message to user when the microphone is accessed for the first time</string>
```

Next, to add permissions so that the app works correctly on an Android device, open the file `android/app/src/main/AndroidManifest.xml` and add the following:

```xml
<!-- Required -->
<uses-permission android:name="android.permission.CAMERA" />

<!-- Include this only if you are planning to use the camera roll -->
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Then, open another file `android/app/build.gradle` and add the following:

```groovy
android {
 ...
 defaultConfig {
 ...
 // insert this line
 missingDimensionStrategy 'react-native-camera', 'general'
 }
}
```

That's it for the installation process for the OS platforms. From the next section, let us continue to build the app.

## Setting up the Camera in a React Native App

In this section, let us first try and test the RNCamera module. Open the `App.js` file and start by adding the following import statements. Nothing fancy here. You just have to import the core React Native components such as `View` and `Alert` as well as `RNCamera` from `react-native-camera`.

```js
import React, { Component } from 'react';
import { StyleSheet, View, Alert } from 'react-native';
import { RNCamera } from 'react-native-camera';
```

Then, create a class component `App` that is going to render the JSX that uses a hardware camera on the device's screen. This going to be done by wrapping the `RNCamera` component inside a `View`.

```js
class App extends Component {
  render() {
    return (
      <View style={styles.container}>
        <RNCamera
          style={{ flex: 1, alignItems: 'center' }}
          ref={ref => {
            this.camera = ref;
          }}
        />
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    backgroundColor: 'black'
  }
});

export default App;
```

After adding the above snippet, make sure you build the app for the OS you are using to test it. I am going to use a real Android device for testing.

```shell
# for iOS
react-native run-ios

# for Android
react-native run-android
```

When testing on an Android device, please make sure that the device is connected via USB, and make sure that USB debugging is enabled as well before you run the previous build command from a terminal window.

After the app has finished building and this process triggers the metro bundler, you are going to get a prompt asking for permission when the app runs for the first time.

![js1](https://i.imgur.com/3QKgfRX.jpg)

This means that the camera is working as expected and now you can leverage this to scan QR codes.

## Reading a QR Code Information

To read the QR code information, you will have to make use of the prop `onGoogleVisionBarcodesDetected`. This prop, with the help of a helper method, can be used to evaluate the value of the scanned QR code.

In the `App.js` file, start by modifying the `RNCamera` component as below.

```js
<RNCamera
  ref={ref => {
    this.camera = ref;
  }}
  style={styles.scanner}
  onGoogleVisionBarcodesDetected={this.barcodeRecognized}
/>
```

Add the corresponding styles for the Camera component in the previously defined `StyleSheet` object.

```js
const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    backgroundColor: 'black'
  },
  // add the following
  scanner: {
    flex: 1,
    justifyContent: 'flex-end',
    alignItems: 'center'
  }
});
```

Then, before the render method, add the helper method `barcodeRecognized` as well the state variable `barcodes` whose initial value is going to be an array.

```js
state = {
  barcodes: []
};

barcodeRecognized = ({ barcodes }) => {
  barcodes.forEach(barcode => console.log(barcode.data));
  this.setState({ barcodes });
};
```

The above helper method is going to update the state variable `barcodes` that can be used to render the value of the QR code scanned using the RNCamera. Let us add two helper methods following the `barcodeRecognized` method. These helper methods are going to be responsible for displaying the information of the QR code.

```js
renderBarcodes = () => (
  <View>{this.state.barcodes.map(this.renderBarcode)}</View>
);

renderBarcode = ({ data }) =>
  Alert.alert(
    'Scanned Data',
    data,
    [
      {
        text: 'Okay',
        onPress: () => console.log('Okay Pressed'),
        style: 'cancel'
      }
    ],
    { cancelable: false }
  );
```

Lastly, to render the Alert box, make sure you add the code below to modify the `RNCamera` component as below.

```js
<RNCamera
  ref={ref => {
    this.camera = ref;
  }}
  style={styles.scanner}
  onGoogleVisionBarcodesDetected={this.barcodeRecognized}
>
  {this.renderBarcodes}
</RNCamera>
```

That's it! Now, let us go back to the app and test it out.

![js3](https://i.imgur.com/m6XlTeg.mp4)

## Conclusion

The `react-native-camera` module can a perfect fit to leverage the device's hardware if you are building cross-platform apps with React Native.

In this tutorial, we only explored the barcode scanning capability, but the same principle applies if you have other goals in mind that also use the device’s camera.

Thanks to great community-built components like RNCamera, React Native apps continue to grow as great alternatives to native mobile apps.

---

Originally published at [Jscrambler](https://blog.jscrambler.com/how-to-use-react-native-camera/)
