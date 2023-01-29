# Providing locally hosted content to WebViews explainer

## Authors:

- Niklas Merz, Apache Software Foundation

## Use-case / Challenge

[The Origin in a WebView for locally hosted content](https://webview-cg.github.io/usage-and-challenges/#the-origin-in-a-webview-for-locally-hosted-content)

## Introduction

WebViews are widely used for building apps on the dominating mobile and desktop platforms. Up to 30% of apps found in the app stores (Apple and Google) are built with frameworks like Apache Cordova and CapacitorJS. For example those two frameworks use one big WebView for providing app developers a native wrapper and some plugins for their Web app. App developers build their Web application and put the HTML, CSS and JavaScript files in one folder. The framework then takes care of building a native app project and bundling the Web code as a native application ready to distribute via the app stores.

Using this approach the native app is responsible for "hosting" the content for the WebView. Traditionally the file protocol (`file:/path/to/content`) was often used to load the Web content from the app bundle. Modern web APIs and some frontend frameworks don't work well with pages loaded from `file:`. This led WebView vendors to build their own APIs for app developers to load their content into the WebView. The existing APIs have each different capabilities and URL schemes, which imposes challenges to developers.

## Goals

The goal of this explainer is to propose a new API for WebViews to serve web content from the local file system. App developers should be able to use the same origin and capabilities to provide their web content to WebViews on different platforms. This also includes an API to intercept and modify requests & responses to certain URLs for delivering web content and exchanging information between the WebView and native layer.

## Non-goals

<!-- If there are "adjacent" goals which may appear to be in scope but aren't,
enumerate them here. This section may be fleshed out as your design progresses, and you encounter necessary technical and other trade-offs. -->

Providing an API that can be used as a network interceptor/proxy for requests to arbitrary URLs is not in scope of this explainer.   

## API proposals

The code examples for the API proposals are written in JavaScript for demonstration purpose only. WebView APIs are provided in one or more languages typically used on the platform respectively. The sample code should just provide an idea how the proposed capabilities should look like.

### [API 1]

[For each related element of the proposed solution - be it an additional WebView API, a new web platform API, a new concept etc., create a section which briefly describes it.]

```js
// Provide example code - not IDL - demonstrating the design of the feature.

// If this API can be used on its own to address a user need,
// link it back to one of the scenarios in the goals section.

// If you need to show how to get the feature set up
// (initialized, or using permissions, etc.), include that too.
```

[Where necessary, provide links to longer explanations of the relevant pre-existing concepts and API.
If there is no suitable external documentation, you might like to provide supplementary information as an appendix in this document, and provide an internal link where appropriate.]


> register custom scheme

### [API 2]

> path handler like android

### [API 3]

> intercept request like iOS

## Key scenarios

[If there are a suite of interacting APIs, show how they work together to solve the key scenarios described.]

### Scenario 1

A popular use case for WebViews are so-called hybrid apps. This app development concept means that a native app consists of one WebView that is used to render the entire apps UI. These apps very often load their web content (HTML, CSS JavaScript) files from the local file system.

Traditionally the `file:` protocol could be used for loading the files just like normal browsers do. A couple of reasons and changes in common web engine behavior and frontend development make this no longer feasible. Namely, some things are:

* Popular JavaScript frameworks like React, Angular etc. build their JavaScript based internal routing systems around "normal" web URLs and won't work with `file:` URLs.
* JavaScript HTTP requests using XMLHttpRequest or fetch (also known as AJAX) don't work well with `file:` URLs. Wen engines consider `file:` URLs a privacy-sensitive context and set the "Origin" of the header to `null` following [RFC-6454](https://www.rfc-editor.org/rfc/rfc6454#section-7.3). This makes [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request difficult or impossible.

The proposed APIs can be used to load local content on a regular, non-privacy-sensitive origin and therefore allow developers to build their web application just like for a regular browser context.

### Scenario 2

> loading content from a remote server (proxy)
> doing native requests from various reasons

In some use cases developers using WebViews might want to intercept or modify requests to certain URLs. Some examples:

* **Session sharing**: If a user logins into a native app and opens a WebView it should be able to use the same session as the native user session and vice versa. The proposed interception API can be used to extract or add authentication information for requests to certain URLs to share them with the native layer to make it possible do HTTP requests with the same authentication information in the native or Web context. These authentication information are typically cookies or auth tokens in headers that could be modified by the interception API.
* **Additional Security**: HTTP Public Key Pinning

## Detailed design discussion

### [Tricky design choice #1]

> discuss "normal" http(s) origin vs custom scheme

[Talk through the tradeoffs in coming to the specific design point you want to make.]

```js
// Illustrated with example code.
```

[This may be an open question,
in which case you should link to any active discussion threads.]

### [Tricky design choice 2]

> discuss WKWebView approach dowsides

## Considered alternatives

[This should include as many alternatives as you can,
from high level architectural decisions down to alternative naming choices.]

### [Alternative 1]

[Describe an alternative which was considered,
and why you decided against it.]

> Webviews loosen restrictions on file: URLs

### [Alternative 2]

[etc.]

## References & acknowledgements

[Your design will change and be informed by many people; acknowledge them in an ongoing way! It helps build community and, as we only get by through the contributions of many, is only fair.]

[Unless you have a specific reason not to, these should be in alphabetical order.]

The proposed APIs and features are largely inspired by the [Android WebViewAssetLoader](https://developer.android.com/reference/androidx/webkit/WebViewAssetLoader) and [iOS WKURLSchemeHandler](https://developer.apple.com/documentation/webkit/wkurlschemehandler). This explainer tries to combine the best of both approaches into common APIs for every WebView platform.

Many thanks for valuable feedback and advice from:

- [Person 1]
- [Person 2]
- [etc.]

