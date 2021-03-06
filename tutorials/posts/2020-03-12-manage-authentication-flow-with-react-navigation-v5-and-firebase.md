---
date: 2020-03-12
title: 'How to manage Authentication flow with react-navigation v5 and Firebase'
template: post
thumbnail: '../thumbnails/react.png'
slug: manage-authentication-flow-with-react-navigation-v5-and-firebase
categories:
  - React Native
  - Firebase
tags:
  - react-native
  - react-navigation
  - firebase
---

Managing User Authentication in your mobile apps can be a fundamental requirement to allow the user to access data only if they are authorized. The `react-navigation` library in its latest version (version 5) allows you to implement a custom authentication flow in React Native apps.

In this tutorial, let us discuss one of the strategies to implement an authentication flow using `react-navigation` library, and `react-native-firebase`. Following along, you are going to set up and configure a Firebase project to implement anonymous sign-in functionality. Lastly, you will be able to take advantage of the latest version of the `react-native-firebase` package.

## Requirements

The following requirements are going to make sure you have a suitable development environment:

- Node.js above `10.x.x` installed on your local machine
- JavaScript/ES6 basics
- `watchman` the file watcher installed
- `react-native-cli` installed through npm or access via `npx`

For a complete walkthrough on how you can set up a development environment for React Native, you can go through [official documentation here](https://reactnative.dev/docs/getting-started).

Also, do note that the following tutorial is going to use the `react-native` version `0.61.5`. Please make sure you are using a version of React Native above `0.60.x`.

## Installing and configuring up react-navigation

To start, create a new React Native project and install the dependencies to set up and use the `react-navigation` library.

```shell
# create a new project
npx react-native init authFlow

cd authFlow

# install core navigation dependencies
yarn add @react-navigation/native @react-navigation/stack react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view
```

From React Native `0.60.x` and higher, linking is automatic so you don't need to run `react-native link`.

To finalize the installation, on iOS, you have to install pods. (_Note: Make sure you have [Cocoapods installed](https://cocoapods.org/)._)

```shell
cd ios/ && pod install

# after pods are installed
cd ..
```

Similarly, on Android, open file `android/app/build.gradle` and add the following two lines in `dependencies` section:

```groovy
implementation 'androidx.appcompat:appcompat:1.1.0-rc01'
implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0-alpha02'
```

Lastly, to save the app from crashing in a production environment, add the following line in `index.js`.

```js
import 'react-native-gesture-handler';
import { AppRegistry } from 'react-native';
import App from './App';
import { name as appName } from './app.json';

AppRegistry.registerComponent(appName, () => App);
```

That's it for configuring up the `react-navigation` library in your React Native app.

## Create a new Firebase Project

To access the Firebase credentials for each mobile OS platform and configure them to use Firebase SDK, create a new Firebase project or use one if you have access already from [Firebase console](http://console.firebase.google.com/), you can skip this step.

Create a new project as shown below.

![hb1](https://miro.medium.com/max/387/1*34zuJZ7AS_yFPTJn523fyQ.png)

Complete the details of your Firebase project:

![hb2](https://miro.medium.com/max/545/1*UDDFg0ZBF7w1jwRRIqxcwA.png)

Click the button **Create project** and you are going to be redirected to the dashboard screen. That's it to create a new Firebase project.

## Add Firebase SDK to React Native app

If you have used `react-native-firebase` version 5 or below, you must have noticed that it was a monorepo that used to manage all Firebase dependencies from one module.

Version 6 of this library wants you to only install those dependencies based on Firebase features that you want to use. For example, in the current app, to support the anonymous login feature you are going to start by the `auth` and core `app` package only.

Also, do note that the core module: `@react-native-firebase/app` is always required.

Open a terminal window to install these dependencies.

```shell
yarn add @react-native-firebase/app @react-native-firebase/auth
```

Go back to the Firebase console of the project and navigate to **Authentication** section from the side menu.

![cb7](https://miro.medium.com/max/257/1*kgdpT5XO0i9HQzQ0OhnPcg.png)

Go to the second tab **Sign-in method** and make sure to enable the **Anonymous** sign-in provider.

![cb8](https://miro.medium.com/max/490/1*pMbNEIK68A2ey5IXpcxJeg.png)

![cb9](https://miro.medium.com/max/975/1*CZVR58kZFU3cf-vhB8aXsg.png)

## Add Firebase credentials to your iOS app

Firebase provides a file called `GoogleService-Info.plist` that contains all the API keys as well as other credentials for iOS devices to authenticate the correct Firebase project.

To get these credentials, go to back to the [Firebase console](http://console.firebase.google.com/), from the dashboard screen of your Firebase project, open **Project settings** from the side menu.

![hb3](https://miro.medium.com/max/241/1*Vh9zbrjZEGjKJl0XDCfFQQ.png)

Go to **Your apps** section and click on the icon `iOS` to select the platform.

![cb1](https://miro.medium.com/max/698/1*6xFdabUZ7wTW1mfR9Ysnrg.png)

Enter the application details and click on **Register app**.

![cb2](https://miro.medium.com/max/635/1*rlPEupDbBl3uqeMmqiMHsQ.png)

Then download the `GoogleService-Info.plist` file as shown below.

![cb3](https://miro.medium.com/max/721/1*irhByL43j7moca3T-ORoxA.png)

Open Xcode, then open the file `/ios/authFlow.xcodeproj` file. Right-click on project name and `Add Files` option, then select the file to add to this project.

![hb4](https://miro.medium.com/max/289/1*0GZAEFT-MKcFcRkJSCc_Bw.png)

![hb5](https://miro.medium.com/max/1135/1*u8MfeTVFjPPNaGLAUF4pQg.png)

Then, open `ios/authFlow/AppDelegate.m` and add the following header.

```c
#import <Firebase.h>
```

Within the `didFinishLaunchingWithOptions` method, add the following `configure` method:

```c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    if ([FIRApp defaultApp] == nil) {
      [FIRApp configure];
    }
```

Lastly, go back to the terminal window to install pods.

```shell
cd ios/ && pod install

# after pods are installed
cd ..
```

Make sure you build the iOS app.

```shell
npx react-native run-ios
```

## Add Firebase credentials to your Android app

For Android devices, Firebase provides a `google-services.json` file that contains all the API keys as well as other credentials for Android devices to authenticate the correct Firebase project.

Go to **Your apps** section and click on the icon `Android` to select the platform.

![cb1](https://miro.medium.com/max/698/1*6xFdabUZ7wTW1mfR9Ysnrg.png)

Then download the `google-services.json` file as shown below.

![cb6](https://miro.medium.com/max/854/1*n52lXWGVa--QwOPFU7gjkQ.png)

Now copy the downloaded JSON file to your React Native project at the following location: `/android/app/google-services.json`.

![hb6](https://miro.medium.com/max/555/1*33PW_A2EILrQRtCMjbfWIg.png)

Then open `android/build.gradle` file and add the following:

```groovy
dependencies {
        // ...
        classpath 'com.google.gms:google-services:4.2.0'
    }
```

Then, open `android/app/build.gradle` file and at the very bottom of this file, add the following:

```groovy
apply plugin: 'com.google.gms.google-services'
```

Make sure you build the Android app.

```shell
npx react-native run-android
```

## Set up a Login Screen

Create a new file called `Login.js` inside the directory `src/screens/`. This screen component is going to be responsible to display a login button and authenticate the user if they have signed out of the app.

Add the following code snippet to this file:

```js
import React from 'react';
import { View, StyleSheet, Text, TouchableOpacity } from 'react-native';

export default function Login() {
  // TODO: add firebase login function later

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Welcome</Text>
      <TouchableOpacity
        style={styles.button}
        onPress={() => alert('Anonymous login')}
      >
        <Text style={styles.buttonText}>Login Anonymously 🔥</Text>
      </TouchableOpacity>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#ffe2ff'
  },
  title: {
    marginTop: 20,
    marginBottom: 30,
    fontSize: 28,
    fontWeight: '500',
    color: '#7f78d2'
  },
  button: {
    flexDirection: 'row',
    borderRadius: 30,
    marginTop: 10,
    marginBottom: 10,
    width: 300,
    height: 60,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#481380'
  },
  buttonText: {
    color: '#ffe2ff',
    fontSize: 24,
    marginRight: 5
  }
});
```

## Add two stack navigators

Inside the directory `src/navigation/` and create two new files:

- `SignInStack.js`
- `SignOutStack.js`

Both of these files are self-explanatory by their names. Their functionality is going to contain screens related to the authenticated state of the app. For example, the `SignOutStack.js` is going to have a [stack navigator](https://heartbeat.fritz.ai/getting-started-with-stack-navigator-using-react-navigation-5-in-react-native-and-expo-apps-4c516becaee1) have a screen file (such as `Login.js`) to show user info when they are not authorized to enter the app.

To get more details about what a stack navigator is and how to use it, check out the previous post [here](https://heartbeat.fritz.ai/getting-started-with-stack-navigator-using-react-navigation-5-in-react-native-and-expo-apps-4c516becaee1).

Open `SignOutStack.js` file and using `NavigatorContainer` and [an instance of `createStackNavigator`](https://heartbeat.fritz.ai/getting-started-with-stack-navigator-using-react-navigation-5-in-react-native-and-expo-apps-4c516becaee1), implement the following code snippet:

```js
import * as React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import Login from '../screens/Login';

const Stack = createStackNavigator();

export default function SignOutStack() {
  return (
    <NavigationContainer>
      <Stack.Navigator headerMode='none'>
        <Stack.Screen name='Login' component={Login} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

This is how navigators are defined declaratively using version 5 of `react-navigation`. It follows a more component based approach, similar to that of `react-router` in web development (only if you are familiar with it).

Add the following code snippet to `SignInStack.js` file:

```js
import * as React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import Home from '../screens/Home.js';

const Stack = createStackNavigator();

export default function SignInStack() {
  return (
    <NavigationContainer>
      <Stack.Navigator headerMode='none'>
        <Stack.Screen name='Home' component={Home} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

If the user is authorized they are going to have access to the `Home` screen. Create another file in `src/screens/` called `Home.js` and add the following code snippet:

```js
import React from 'react';
import { View, StyleSheet, Text, TouchableOpacity } from 'react-native';

export default function Home() {
  // TODO: add firebase sign-out and user info function later

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Welcome user!</Text>
      <TouchableOpacity style={styles.button} onPress={() => alert('Sign out')}>
        <Text style={styles.buttonText}>Sign out 🤷</Text>
      </TouchableOpacity>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#ffe2ff'
  },
  title: {
    marginTop: 20,
    marginBottom: 30,
    fontSize: 28,
    fontWeight: '500',
    color: '#7f78d2'
  },
  button: {
    flexDirection: 'row',
    borderRadius: 30,
    marginTop: 10,
    marginBottom: 10,
    width: 160,
    height: 60,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#481380'
  },
  buttonText: {
    color: '#ffe2ff',
    fontSize: 24,
    marginRight: 5
  }
});
```

## Set up an authentication flow

To complete the navigation in the current React Native app, let us set up an authentication flow in this section. Create a new file `src/navigation/AuthNavigator.js` and to import the following as well as both stack navigators created in the previous section.

```js
import React, { useState, useEffect, createContext } from 'react';
import auth from '@react-native-firebase/auth';
import SignInStack from './SignInStack';
import SignOutStack from './SignOutStack';
```

Create an `AuthContext` that is going to expose the user data to only those screens when the user successfully logs in, that is, the screens that are part of the `SignInStack` navigator.

```js
export const AuthContext = createContext(null);
```

Then, define the state variables `initializing` and `user` inside the functional component `AuthNavigator`. The `initializing` state variable is going to be `true` by default and keeps track of the changes in the authentication state. It will be false when the user's authentication state changes. The change in the state is going to be handle by the helper method, `onAuthStateChanged`.

It is important to subscribe to the auth changes when this functional component is mounted. This is done by using the `useEffect` hook. When this component unmounts, unsubscribe it.

Lastly, make sure to pass the value of `user` data using the `AuthContext.Provider`. Here is the complete snippet:

```js
export default function AuthNavigator() {
  const [initializing, setInitializing] = useState(true);
  const [user, setUser] = useState(null);

  // Handle user state changes
  function onAuthStateChanged(result) {
    setUser(result);
    if (initializing) setInitializing(false);
  }

  useEffect(() => {
    const authSubscriber = auth().onAuthStateChanged(onAuthStateChanged);

    // unsubscribe on unmount
    return authSubscriber;
  }, []);

  if (initializing) {
    return null;
  }

  return user ? (
    <AuthContext.Provider value={user}>
      <SignInStack />
    </AuthContext.Provider>
  ) : (
    <SignOutStack />
  );
}
```

Now, to make it work, open `App.js` file and modify it as below:

```js
import React from 'react';
import AuthNavigator from './src/navigation/AuthNavigator';

const App = () => {
  return <AuthNavigator />;
};

export default App;
```

You are going to get a similar output as shown below:

![hb7](https://miro.medium.com/max/350/1*40hopOAqTJVyRY2mi_1VTQ.png)

## Add the login functionality

Open `screens/Login.js` file and start by importing the `auth` module.

```js
// ... rest of the import statements
import auth from '@react-native-firebase/auth';
```

Inside the `Login` functional component, add a helper method `signIn()` that is going to be triggered when the user presses the sign-in button on this screen. This method is going to be an asynchronous function.

```js
async function signIn() {
  try {
    await auth().signInAnonymously();
  } catch (e) {
    switch (e.code) {
      case 'auth/operation-not-allowed':
        console.log('Enable anonymous in your firebase console.');
        break;
      default:
        console.error(e);
        break;
    }
  }
}
```

Add this helper method as the value of the prop`onPress` for `TouchableOpacity`.

```js
<TouchableOpacity style={styles.button} onPress={signIn}>
  {/* rest remains same */}
</TouchableOpacity>
```

## Add log out functionality

To add a log out functionality, open `screens/Home.js` file and import Firebase `auth` module as well as `AuthContext` from `navigator/AuthNavigator.js` file. The `AuthContext` is going to help us verify that the user is being signed in. Also, import the `useContext` hook from `React`.

```js
import React, { useContext } from 'react';
import { View, StyleSheet, Text, TouchableOpacity } from 'react-native';
import auth from '@react-native-firebase/auth';
import { AuthContext } from '../navigation/AuthNavigator';
```

Next, in the functional component `Home` using the hook, store the `user` object coming from Firebase. Also, define an asynchronous helper method `logOut()`.

Make sure in the JSX returned from this component, the `uid` or the unique user id from the Firebase is displayed as well as the value of the prop `onPress` is the helper method.

```js
export default function Home() {
  const user = useContext(AuthContext);

  async function logOut() {
    try {
      await auth().signOut();
    } catch (e) {
      console.error(e);
    }
  }

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Welcome {user.uid}!</Text>
      <TouchableOpacity style={styles.button} onPress={logOut}>
        <Text style={styles.buttonText}>Sign out 🤷</Text>
      </TouchableOpacity>
    </View>
  );
}
```

Here is the complete output of the app:

![hb8](https://miro.medium.com/max/373/1*ngW6EKFr8f0kFvla917CcQ.gif)

At this point, if you head back to the Firebase console, you can verify the same user id of the last logged in user.

![hb9](https://miro.medium.com/max/939/1*9liE9HI37moJK__zO_PAWw.png)

## Conclusion

Thanks for going through this tutorial. I hope you had fun integrating the latest version of `react-navigation` and `react-native-firebase` libraries.

For more info to manage Authentication flows, you can refer to the official documentation of `react-navigation` [here](https://reactnavigation.org/docs/auth-flow).

---

_Originally published at [Heartbeat](https://heartbeat.fritz.ai/how-to-manage-authentication-flows-in-react-native-with-react-navigation-v5-and-firebase-860f57ae20d3)_.
