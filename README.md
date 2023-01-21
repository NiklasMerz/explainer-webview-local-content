# Providing locally hosted content to WebViews explainer

## Authors:

- Niklas Merz, Apache Software Foundation

## Use-case / Challenge

[The Origin in a WebView for locally hosted content](https://webview-cg.github.io/usage-and-challenges/#the-origin-in-a-webview-for-locally-hosted-content)

## Introduction

WebViews are widely used for building apps on the dominating mobile and desktop platforms. Up to 30% of apps found in the app stores (Apple and Google) are built with frameworks like Apache Cordova and CapacitorJS. For example those two frameworks use one big WebView for providing app developers a native wrapper and some plugins for their Web app. App developers build their Web application and put the HTML, CSS and JavaScript files in one folder. The framework then takes care of building a native app project and bundling the Web code as a native application ready to distribute via the app stores.

Using this approach the native app is responsible for "hosting" the content for the WebView. In the past the file protocol (`file:/path/to/content`) was often used to load the Web content from the app bundle. Modern web APIs and some frontend frameworks don't work well with pages loaded from `file:`. This led WebView vendors to build APIs for app developers to load their content into the WebView. The existing APIs have each different capabilities and origins, which imposes challenges to developers.

## Goals [or Motivating Use Cases, or Scenarios]

[What is the **end-user need** which this project aims to address?]

> - Different capabilities to modify/intercept requests & response

The goal of this explainer is to propose a new API for WebViews to serve web content from the local file system. App developers should be able to use the same origin and capabilities to provide their web content to WebViews on different platforms.

## Non-goals

[If there are "adjacent" goals which may appear to be in scope but aren't,
enumerate them here. This section may be fleshed out as your design progresses, and you encounter necessary technical and other trade-offs.]

> proxy requests?

## API proposals

The code examples for the API proposal are written in JavaScript for demonstration purpose only. WebView APIs are typically provided in one or more languages typically used on the platform respectively. The sample code should just provide an idea how the proposed capabilities should look like.

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

[If this is already specced, link to the relevant section of the spec.]

[If spec work is in progress, link to the PR or draft of the spec.]

> register custom scheme

### [API 2]

[etc.]

> intercept request

## Key scenarios

[If there are a suite of interacting APIs, show how they work together to solve the key scenarios described.]

### Scenario 1

> loading content from the file system / app bundle

### Scenario 2

> loading content from a remote server (proxy)
> doing native requests from various reasons

## Detailed design discussion

### [Tricky design choice #1]

> discuss "normal" origin vs custom scheme

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

Many thanks for valuable feedback and advice from:

- [Person 1]
- [Person 2]
- [etc.]

