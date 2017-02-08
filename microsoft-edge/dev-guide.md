---
description: This guide provides an overview of the developer features and standards included in Microsoft Edge.
title: Dev guide
author: erikadoyle
ms.author: edoyle
ms.date: 02/08/2017
ms.topic: article
ms.prod: microsoft-edge
keywords: edge, web development, html, css, javascript, developer
---

# Microsoft Edge Developer Guide
This guide provides an overview of the developer features and standards included in Microsoft Edge.

## What's new in EdgeHTML 15
Here are the changes shipped with the current release of the Microsoft Edge platform,
 as of the [Windows Creators Update(MM/YYYY)](). For changes in Windows Insider Preview builds, see
 the Microsoft Edge [Changelog](https://developer.microsoft.com/en-us/microsoft-edge/platform/changelog/).

### New features

#### CSS Custom Properties
Microsoft Edge now supports [CSS Custom Properties](https://drafts.csswg.org/css-variables/), a.k.a CSS Variables. CSS Variables allow you to create custom CSS properties that can be reused throughout stylesheets to help reduce the amount of duplicate data, like repeated colors. Using CSS Variables is simple: 

```css
/* define a custom property by using two dashes and assign it a value */
body {   
   --default-color: #3390b1
}

/* reference it in your stylesheet with the "var()" function */
h1 { 
   color: var(--default-color); 
}
```  
You can test out CSS Custom Properties in Microsoft Edge build #####+. 


#### Intersection Observer
Microsoft Edge fully supports the [Intersection Observer API](https://wicg.github.io/IntersectionObserver/) specification beginning in Build #####+. The Intersection Observer API allows you to asynchronously query the position and visibility of DOM elements relative to other elements or the global viewport. This API eliminates the need for custom expensive code by creating a method to efficiently notify elements when they are in view. 

#### Payment Request API
The [Payment Request API](http://www.w3.org/TR/payment-request/) is now supported in Microsoft Edge, enabling simpler checkout and payments on the web with Microsoft Wallet on Windows 10 PCs and Phones. This API enables Microsoft Edge to act as an intermediary between merchants, consumers, and the payment methods (e.g. credit cards) that consumers have stored in the cloud. For more information on the Payment Request API, check out the blog post: [Simpler web payments: Introducing the Payment Request API](https://blogs.windows.com/msedgedev/2016/12/15/payment-request-api-edge/) and the [Payment Request API](https://docs.microsoft.com/en-us/microsoft-edge/dev-guide/device/payment-request-api) Dev Guide article. 

#### WebRTC

#### WebVR

### Updated features

#### Content Security Policy (Level 2)
Sites already using CSP 1 should continue to work with Microsoft Edge support for CSP 2, however it's best to switch any `frame-src` directives that load worker scripts to the new `child-src` directive to future-proof your site. (In CSP 3, `frame-src` will no longer apply to workers.) CSP 2 also adds the following:

1. New directives: `base-uri`, `child-src`, `form-action`, `frame-ancestors` and `plugin-types`. See [Edge supported CSP directives](./dev-guide/security/content-security-policy.md) for more.

2. Workers support: Background worker scripts are governed by their own policy, separate from the policy of the document loading them. As with host documents, you can set the CSP for a worker in the response header. Also new in CSP 2 is that  `allow-scripts` and `allow-same-origin` flags of the `sandbox` directive now affect worker thread creation.

3. Inline scripts and styles: CSP 2 allows for the execution of inline scripts and style blocks by providing nonces and hashes as a whitelisting mechanism. Nonces are random base-64 values generated on each page load that appears in both the CSP policy and in the script tags in the page. When the page is dynamically generated on load, the server generates a nonce value, inserts it into the NonceToken in the page and also declares it in the Content Security Policy HTTP header. Hashes are static values generated (via *sha256*, *sha384* or *sha512* algorithms) from the content of a `<script>` or `<style>` element that are then specified (via `script-src` or `style-src` directives) in the CSP policy.

4. CSP violation reporting: A new event, SecurityPolicyViolationEvent is now fired upon CSP violations. The earlier mechanism for CSP reporting, `report-uri`, continues to be supported. Several new fields have been added to the violation reports common to both, including `effectiveDirective` (the policy that was violated), `statusCode` (the HTTP response code), `sourceFile` (the URL of the offending resource), `lineNumber`, and `columnNumber`.

#### Web Authentication

#### WebDriver

#### WebGL

## Previous EdgeHTML releases
[EdgeHTML 13 / Windows build 14393 (11/2015)](https://blogs.windows.com/msedgedev/2015/11/16/introducing-edgehtml-13-our-first-platform-update-for-microsoft-edge/#TuusgqsWj8X6pDcD.97)

[EdgeHTML 14 / Windows build 10240 (7/2016)](https://blogs.windows.com/msedgedev/2016/08/04/introducing-edgehtml-14/#6xVDezg4bfyDyIWJ.97)
