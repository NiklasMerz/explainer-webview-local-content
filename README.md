# Providing locally hosted content to WebViews explainer

## Authors:

- Niklas Merz, Apache Software Foundation

## Use-case / Challenge

[The Origin in a WebView for locally hosted content](https://webview-cg.github.io/usage-and-challenges/#the-origin-in-a-webview-for-locally-hosted-content)

## Introduction

WebViews are widely used for building apps on the dominating mobile and desktop platforms. Up to 30% of apps found in the app stores (Apple and Google) are built with frameworks like Apache Cordova and CapacitorJS. For example those two frameworks use one big WebView for providing app developers a native wrapper and some plugins for their Web app. App developers build their Web application and put the HTML, CSS and JavaScript files in one folder. The framework then takes care of building a native app project and bundling the Web code as a native application ready to distribute via the app stores.

Using this approach the native app is responsible for "hosting" the content for the WebView. Traditionally the file protocol (`file:/path/to/content`) was often used to load the Web content from the app bundle. Modern web APIs and some frontend frameworks don't work well with pages loaded from `file:`. This led WebView vendors to build their own APIs for app developers to load their content into the WebView. The existing APIs have each different capabilities and URL schemes, which imposes challenges to developers.

## Goals

The goal of this explainer is to propose a new API for WebViews to serve web content from the local file system. App developers should be able to use the same origin and capabilities to provide their web content to WebViews on different platforms. This also includes an API to intercept and modify requests & responses to certain URLs for delivering web content and sharing HTTP requests between the WebView and native layer.

## Non-goals

<!-- If there are "adjacent" goals which may appear to be in scope but aren't,
enumerate them here. This section may be fleshed out as your design progresses, and you encounter necessary technical and other trade-offs. -->

Providing an API that can be used as a network interceptor/proxy for requests to arbitrary URLs is not in scope of this explainer.   

## API proposals

The code examples for the API proposals are written in JavaScript for demonstration purpose only. WebView APIs typically are provided in one or more languages typically used on the platform respectively. The sample code should just provide an idea how the proposed capabilities should look like.

### Register custom scheme

A WebView implementation should allow registering custom schemes like `appname://`, `localcontent:\\` etc. to be used for hosting the local files.

```js

const webview = new WebView()

// webview should intercept request to "myapp://"
webview.registerScheme('myapp')

```

<!-- Where necessary, provide links to longer explanations of the relevant pre-existing concepts and API.
If there is no suitable external documentation, you might like to provide supplementary information as an appendix in this document, and provide an internal link where appropriate. -->


### Intercept & modify request + response

This API allows you to interact with HTTP request to your custom scheme.

```js

webview.shouldInterceptRequest((request) =>{

    // Intercept only request to "myapp://localcontent" and create new reponse
    if (request.hostname == 'localcontent') {

        // Here you could build a new reponse from locally loaded files or fetched files from the native layer
        response = new Reponse('MY HTML CONTENT HERE');
        reponse.setCookie('intercepted', 'true')
        reponse.setHeader('Intercepted', 'true');

        return reponse;
    }

    // Request should not be handled here and is handled normally by the WebView
    return undefined;
})

```

The callback function of `shouldInterceptRequest` receives all requests to registered custom schemes and returns the desired response. If no response is given (null value) the WebView falls back to its regular behavior.

### Default asset loader

There could be an integrated API for just loading files from a directory with no further interaction needed.

```js

const webview = new WebView()

webview.registerPathHanlder('myapp', '/path/to/local/files')

```

## Key scenarios

<!--If there are a suite of interacting APIs, show how they work together to solve the key scenarios described. -->

### Hybrid Apps

A popular use case for WebViews are so-called hybrid apps. This app development concept means that a native app consists of one WebView that is used to render the entire apps UI. These apps very often load their web content (HTML, CSS JavaScript) files from the local file system.

Traditionally the `file:` protocol could be used for loading the files just like normal browsers do. A couple of reasons and changes in common web engine behavior and frontend development make this no longer feasible. Namely, some things are:

* Popular single page application frameworks like React, Angular etc. build their JavaScript based internal routing systems using HTML5 round and won't work with `file:` URLs.
* JavaScript HTTP requests using XMLHttpRequest or fetch (also known as AJAX) don't work well with `file:` URLs. Web engines consider `file:` URLs a privacy-sensitive context and set the "Origin" of the header to `null` following [RFC-6454](https://www.rfc-editor.org/rfc/rfc6454#section-7.3). This makes [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) request difficult or impossible. Hybrid apps often use local files for the UI hosted on one local origin and access REST APIs hosted on another remote origin.

The proposed APIs can be used to load local content on a regular, non-privacy-sensitive origin and therefore allow developers to build their web application just like for a regular browser context.

> TODO explain flow with code examples

### Custom HTTP requests in the native layer

In some use cases developers using WebViews might want to intercept or modify requests to certain URLs. Some examples:

* **Session sharing**: If a user logins into a native app and opens a WebView it should be able to use the same session as the native user session and vice versa. The proposed interception API can be used to extract or add authentication information for requests to certain URLs to share them with the native layer to make it possible do HTTP requests with the same authentication information in native or Web context. Authentication information are typically cookies or auth tokens in headers that could be modified by the interception API.
* **Additional Security**: HTTP Public Key Pinning
* **InAppUpdates**: Apps might want to load content from servers instead of the local files. This could be for offline mode implementations or getting newer versions without updating the complete app.

## Detailed design discussion

### Custom scheme vs HTTP(S) Origin

<!-- Talk through the tradeoffs in coming to the specific design point you want to make. -->

There are different options for the special URL that should be handled by this API. Both options are used right now by existing WebView implementations.

```js
const webview = new WebView()

webview.registerScheme('myapp') // Used by iOS WKURLSchemeHandler

// or 

webview.registerScheme('https://localcontent.mydomain.com') // Used by Android WebViewAssetLoader
```

The custom scheme variant is straightforward, because this avoids conflicts with existing internet addresses. Officially registered and non-standard custom schemes are widely used for different purposes in apps.

The standard HTTP(S) scheme probably offers better flexibility and compatability with Web APIs and standards such as CSP and CORS. It also allows use-cases where the WebViews falls back or overwrites certain URLs.

**Both variants could be allowed.**

> This design decision is up for discussion: https://github.com/NiklasMerz/explainer-webview-local-content/discussions/1

<!-- This may be an open question,
in which case you should link to any active discussion threads. -->

### Origin

Each registered origin like `https://foo` and `https://bar` should be treated as their own origin like "regular" Web pages in browsers.

### Proxy API

> URL handler can be used for a proxy-like implementation of all web requests from the WebView through the native layer

The proposed API can be used to proxy Web requests by rewriting the URL to one used by an handler and using that handler to create HTTP requests in the native layer.

## Considered alternatives

<!-- This should include as many alternatives as you can,
from high level architectural decisions down to alternative naming choices. -->

### WebViews don't consider the `file:` protocol as privacy-sensitive content.

> TODO research standards & Android + iOS WebView changes 

[RFC 6454 The Web Origin Concept](https://www.rfc-editor.org/rfc/rfc6454#section-7.3) does not define privacy-sensitive contexts therefore WebViews could consider setting the Origin header with a valid value to avoid the issues with CORS requests as mentioned before.


## References & acknowledgements

<!-- Your design will change and be informed by many people; acknowledge them in an ongoing way! It helps build community and, as we only get by through the contributions of many, is only fair.
Unless you have a specific reason not to, these should be in alphabetical order. -->

The proposed APIs and features are largely inspired by [Androids WebViewAssetLoader](https://developer.android.com/reference/androidx/webkit/WebViewAssetLoader) and [iOS' WKURLSchemeHandler](https://developer.apple.com/documentation/webkit/wkurlschemehandler). This explainer tries to combine the best of both approaches into common APIs for every WebView platform.


Many thanks for valuable feedback and advice from:

* Rayan Kanso
