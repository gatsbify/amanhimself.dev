---
date: 2020-01-09
title: 'How to build React Native apps with GraphQL and Apollo'
template: post
thumbnail: '../thumbnails/expo.png'
slug: integrate-react-native-graphql-apollo-client
categories:
  - Expo
  - React Native
tags:
  - expo
  - react-native
  - apollo
---

GraphQL is described as a query language for APIs. It is also considered as an alternative to REST and has been adapted more frequently in the last few years. Do you have a GraphQL endpoint already setup but you are looking forward to gaining some insight on how to gracefully consume this endpoint in a React Native app?

Together, let us build a demo app that leverages an integration between Apollo Client, React Native and [Expo](https://expo.io/).

[Apollo](https://www.apollographql.com/) has an entire ecosystem based on to build GraphQL applications. It could be used to develop client-side and server-side apps separately. Apollo has more features and support than its open-source competitors in GraphQL for JavaScript world for now.

## Objectives

The main objectives of this tutorial going to cover are:

- Learn how to integrate the Apollo Client in a React Native app
- Fetch data from a REST Endpoint with Apollo using GraphQL query language
- Show a loading indicator when results are being fetched
- Display results from the remote API endpoint

## Requirements

Before we get started, make sure you have installed the following on your local development machine.

- Nodejs <= `10.x.x`
- `expo-cli`
- Access to API Key at [NewsAPI.org](https://newsapi.org/) (_it's free_)

Do note that, to follow along with this tutorial, you need some basic knowledge of React Native and Expo SDK. You are also required to have [Expo Client](https://expo.io/tools) installed either on a simulator device or a real device.

Throughout this tutorial, I am going to rely on and use the iOS simulator. The demo app we are constructing should work on Android devices as well.

## Generating an Expo app

Create a new React Native/Expo app using the following command. Make sure to navigate to the directory once the project has generated.

```shell
npx expo init rn-apollo-demo

cd rn-apollo-demo
```

When generating an Expo app, you are prompted some questions by the cli interface as shown below. Make sure you choose `expo-template-blank`. The rest of the questions can have default answers as suggested.

[fl1]

This tutorial assumes that you are using the latest Expo SDK version (`36.x.x` and above ).

The next step is to install dependencies that we are going to use to build this app. You will learn about each individual dependency whenever necessary in the rest of the tutorial. Do note that, some of these dependencies are peer dependencies and you might not use them directly. Open a terminal window and install the following.

```shell
yarn add apollo-client apollo-cache-inmemory graphql-tag apollo-link-rest apollo-link graphql graphql-anywhere qs
```

After the dependencies have installed, open `App.js` file from the root of the project in your favorite code editor or IDE. Let us replace some of the default contents with some meaningful UI components such as custom header.

```js
// App.js
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

const Header = () => (
  <View style={styles.header}>
    <Text style={styles.headerText}>News App</Text>
  </View>
);

function App() {
  return (
    <View style={styles.container}>
      <Header />
      <View style={styles.contentContainer}>
        <Text>Open up App.js to start working on your app!</Text>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff'
  },
  header: {
    marginTop: 50,
    alignItems: 'center',
    borderBottomWidth: StyleSheet.hairlineWidth
  },
  headerText: {
    marginBottom: 5,
    fontSize: 30,
    fontWeight: 'bold'
  },
  contentContainer: {
    paddingHorizontal: 20,
    paddingVertical: 20
  }
});

export default App;
```

To make sure everything works, open the terminal window. Execute the command `expo start` that triggers the Expo client app to run. Press `i` if you are using an iOS simulator or `a` if you are using an android emulator.

When the App component renders for the first time, you are going to get the following output:

[fl2]

## How to consume a REST endpoint using Apollo Client

With the base app running, you are ready to build further. Let us explore a way to configure the Apollo client in our app in this section. Create a new file called `Client.js` inside a directory `src/graphql`. The configuration part is going to fit inside this file. Import the following statements to begin the configuration process.

```js
// src/graphql/Client.js
import { ApolloClient } from 'apollo-client';
import { InMemoryCache } from 'apollo-cache-inmemory';
import { RestLink } from 'apollo-link-rest';
```

Three different packages are being imported in the above code snippet. The `apollo-client` and `apollo-cache-inmemory` are used to integrate GraphQL client in a React or React Native app. Another package required to make it work is called `apollo-link`. But in our app, we are going to use a variant called `apollo-link-rest`.

_Why the variant?_ The reason being is that this package allows the app to use third-party APIs that do not have a GraphQL endpoint. That means, they tend to have a REST endpoint. One real-time API is [NewsAPI](https://newsapi.org/). This package is going to help us transmit the query to the REST endpoint in a GraphQL query.

In the `Client.js` file, start by adding a link from the constructor `RestLink` and pass the API key as field inside `headers` object. This object represents the value to be sent as headers on a request.

```js
const restLink = new RestLink({
  uri: 'https://newsapi.org/v2/',
  headers: {
    Authorization: '47e036d83ccc4058b1f85362bc2be1f4'
  }
});
```

Lastly, add the below configuration with the default cache and `RestLink` to complete the configuration of Apollo Client.

```js
export const client = new ApolloClient({
  link: restLink,
  cache: new InMemoryCache()
});
```

## Fetching results from a Rest endpoint using Apollo

Create a new file called `Queries.js` inside `src/graphql` directory. This file is going to contain the structure of the GraphQL query which is eventually going to fetch the result from the API.

Start by importing `gql` from `graphql-tag`.

```js
import gql from 'graphql-tag';
```

Export a new query called `Headlines` as shown in the snippet below. This query is going to fetch top articles with fields such as title, the published location, the URL of the article and so on. Using the `@rest` directive, Apollo client knows how to parse the query.

```js
export const Headlines = gql`
  query TopHeadlines {
    headlines
      @rest(
        type: "HeadlinesPayload"
        path: "top-headlines?country=us&category=technology"
      ) {
      totalResults
      articles @type(name: "ArticlePayload") {
        title
        publishedAt
        url
        urlToImage
        source @type(name: "SourcePayload") {
          name
        }
      }
    }
  }
`;
```

## Fetching results from the query

To use the previously created query, import it as well as the configured Apollo client inside the `App.js` file.

```js
import { client } from './src/graphql/Client';
import { Headlines } from './src/graphql/Queries';
```

Using the query `Headlines` the result should be fetched from the API whenever the component mounts as well as on the initial render of the app. This can be done by using the React Hook `useEffect`.

Create a handler method `requestHeadlines` to invoke the query using Apollo Client. Invoking the query is simply done adding a promise. I am going to `console.log` the result from the query for now.

```js
// do ont forget to import useEffect
import React, { useEffect } from 'react';

// inside App functional component
useEffect(() => {
  requestHeadlines();
}, []);

const requestHeadlines = () => {
  client
    .query({
      query: Headlines
    })
    .then(response => {
      console.log('RESPONSE ==>', response);
    })
    .catch(error => {
      console.log('ERROR ==>', error);
    });
};
```

With the Expo client, there is an option to Debug JS remotely. It opens the console tab in browser’s developer tools and lets you see results of log statements (just like in web development).

Here is the result of the above query. When invoked, it fetches 20 headline objects in an array with all requested fields.

[fl3]

## Add a loading indicator

In the last image, in the response, you will find a field called `loading` that indicates are the results being fetched or the response is complete. Using this can be helpful when adding an activity indicator to display the same message to the end-user in a real mobile app.

Start, by importing the `ActivityIndicator` component from react-native. Also, to set a state for the loading indicator, import the hook `useState`.

```js
import React, { useState, useEffect } from 'react';
import { StyleSheet, Text, View, ActivityIndicator } from 'react-native';
```

Inside the `App` component define the state for `loading`.

```js
const [loading, setLoading] = useState(true);
```

Modify the return statement by adding an if-else statement based on the boolean value of the `loading`.

```js
if (loading) {
  return (
    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
      <ActivityIndicator size='large' />
    </View>
  );
} else {
  return (
    <View style={styles.container}>
      <Header />
      <View style={styles.contentContainer}>
        <Text>Open up App.js to start working on your app!</Text>
      </View>
    </View>
  );
}
```

After this step, you are going to get the following output:

[fl4]

To make it stop and behave in the way the app requires, update the state of `loading` when the promise resolves inside the query handler method `requestHeadlines`.

```js
.then(response => {
 console.log('RESPONSE ==>', response)
 setLoading(response.loading)
 })
```

Refresh the Expo client and you will notice that after a few seconds, when the data is fetched, the loading indicator disappears and the screen UI is displayed as before.

## Display headlines from the API

You are all set to display individual article component that is being fetched from the API endpoint. Add another state variable called `articles`. To render a list of data, let’s use the FlatList component from react-native.

```js
import {
  StyleSheet,
  Text,
  View,
  ActivityIndicator,
  FlatList
} from 'react-native';

// inside App component add another state variable called articles
const [articles, setArticles] = useState([]);
```

Update the articles state with the array from the response when the promise gets resolved inside the handler method `requestHeadlines`.

```js
const requestHeadlines = () => {
  client
    .query({
      query: Headlines
    })
    .then(response => {
      console.log('RESPONSE ==>', response);
      setLoading(response.loading);
      setArticles(response.data.headlines.articles);
    })
    .catch(error => {
      console.log('ERROR ==>', error);
    });
};
```

The `FlatList` component requires three attributes to render a list of data items.

- `data` is an array of data that is provided by the state variable `articles`.
- `renderItem` contains the JSX for each item in the data array for which let’s create a new component called `Article` in a separate file in the next section.
- `keyExtractor` is used to extract a unique key for a given item at the specified index for which let’s use each article’s URL for that is going to be unique.

```js
return (
  <View style={styles.container}>
    <Header />
    <View style={styles.contentContainer}>
      <FlatList
        data={articles}
        renderItem={({ item }) => <Article {...item} />}
        keyExtractor={item => `${item.url}`}
      />
    </View>
  </View>
);
```

## Add an Article component

Inside the `src/components` directory create a new file called `Article.js`. This component is going to display the title and source of each headline.

The `Article` component is going to be a presentation component that receives everything from the `App` component as props. Add the following to the file.

```js
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

const Article = ({ title, source }) => (
  <View style={styles.content}>
    <Text style={styles.source}>{source.name}</Text>
    <Text style={styles.title}>{title}</Text>
  </View>
);

const styles = StyleSheet.create({
  content: {
    marginLeft: 10,
    flex: 1
  },
  source: {
    color: '#3d3c41',
    fontSize: 14,
    fontWeight: '500',
    marginBottom: 3
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    marginBottom: 15
  }
});

export default Article;
```

Import the `Article` component in `App.js` file for it to work.

```js
import Article from './src/components/Article';
```

Go to the simulator or the device that is running the Expo client and you are going to see several headlines being fetched.

[fl5]

## Conclusion

That's it! Congratulations to you for sticking around till the end. I hope this post serves as the basis of your adventure in building React Native apps with GraphQL and Apollo.

---

Originally published at [Newline.co/Fullstack.io](https://www.newline.co/@amandeepmittal/how-to-build-react-native-apps-with-graphql-and-apollo--d74eb12e)
