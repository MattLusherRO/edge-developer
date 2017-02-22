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
 as of the [Windows Creators Update(MM/YYYY), build #####](). For changes in Windows Insider Preview builds, see
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
EdgeHTML 15 introduces the [Intersection Observer API](https://wicg.github.io/IntersectionObserver/) specification. The Intersection Observer API allows you to asynchronously query the position and visibility of DOM elements relative to other elements or the global viewport. This API eliminates the need for custom expensive code by creating a method to efficiently notify elements when they are in view. 

#### Payment Request API
The [Payment Request API](http://www.w3.org/TR/payment-request/) is now supported, enabling simpler checkout and payments on the web with Microsoft Wallet on Windows 10 PCs and Phones. This API enables Microsoft Edge to act as an intermediary between merchants, consumers, and the payment methods (e.g. credit cards) that consumers have stored in the cloud. For more information on the Payment Request API, check out [Simpler web payments: Introducing the Payment Request API](https://blogs.windows.com/msedgedev/2016/12/15/payment-request-api-edge/) and the [Payment Request API](https://docs.microsoft.com/en-us/microsoft-edge/dev-guide/device/payment-request-api) developer guide. 

#### WebRTC and interoperable RTC video codec support

EdgeHTML 15 supports a subset of the WebRTC 1.0 API for interoperability with applications built with earlier versions of the W3C WebRTC-PC API circa 2015. See the [WebRTC API reference](https://msdn.microsoft.com/library/mt806139(v=vs.85).aspx) for details.

To take advantage of our most advanced features in peer-to-peer audio and video communication, we recommend using the [Object Real-Time Communication) API](./dev-guide/realtime-communication/object-rtc-api.md). The ORTC API is better suited for situations where you want to set up group audio and video calls, or directly control individual transport, sender, and receiver objects.

The Microsoft Edge supports both H.264/AVC and VP8 video with ORTC and WebRTC 1.0, and provides the following features in support of both codec types: [abs-send-time](https://webrtc.org/experiments/rtp-hdrext/abs-send-time/), [goog-remb](https://tools.ietf.org/html/draft-alvestrand-rmcat-remb-03), [Picture Loss Indication and Generic NACK feedback](https://tools.ietf.org/html/rfc4585), [RTP Retransmission](https://tools.ietf.org/html/rfc4588). 

For more info, see [Introducing WebRTC 1.0 and interoperable real-time communications in Microsoft Edge](https://blogs.windows.com/msedgedev/2017/01/31/introducing-webrtc-microsoft-edge/#k8XMeWKyZDQDPYR4.97).

#### WebVR

Microsoft Edge now has support for WebVR, an experimental API that connects virtual reality (VR) head mounted displays and Edge. This connection enables VR content to be experienced within a website, meaning immersive VR experiences are no longer limited to desktop applications. 

Virtual reality in Edge is handled with WebGL, a JavaScript API for rendering 3D and 2D graphics. WebGL applications and applications built with WebGL libraries like Three.JS are supported. Once connected, WebVR sends visuals corresponding to the position and sensor information around the headset. The WebVR API also supports gamepad controls thanks to an extensions to the [Gamepad API](./dev-guide/dom/gamepad-api.md).

### Updated features

#### Content Security Policy (Level 2)
Sites already using CSP 1 should continue to work with Microsoft Edge support for CSP 2, however it's best to switch any `frame-src` directives that load worker scripts to the new `child-src` directive to future-proof your site. (In CSP 3, `frame-src` will no longer apply to workers.) CSP 2 also adds the following:

1. New directives: `base-uri`, `child-src`, `form-action`, `frame-ancestors` and `plugin-types`. See [Edge supported CSP directives](./dev-guide/security/content-security-policy.md) for more.

2. Workers support: Background worker scripts are governed by their own policy, separate from the policy of the document loading them. As with host documents, you can set the CSP for a worker in the response header. Also new in CSP 2 is that  `allow-scripts` and `allow-same-origin` flags of the `sandbox` directive now affect worker thread creation.

3. Inline scripts and styles: CSP 2 allows for the execution of inline scripts and style blocks by providing nonces and hashes as a whitelisting mechanism. Nonces are random base-64 values generated on each page load that appears in both the CSP policy and in the script tags in the page. When the page is dynamically generated on load, the server generates a nonce value, inserts it into the NonceToken in the page and also declares it in the Content Security Policy HTTP header. Hashes are static values generated (via *sha256*, *sha384* or *sha512* algorithms) from the content of a `<script>` or `<style>` element that are then specified (via `script-src` or `style-src` directives) in the CSP policy.

4. CSP violation reporting: A new event, SecurityPolicyViolationEvent is now fired upon CSP violations. The earlier mechanism for CSP reporting, `report-uri`, continues to be supported. Several new fields have been added to the violation reports common to both, including `effectiveDirective` (the policy that was violated), `statusCode` (the HTTP response code), `sourceFile` (the URL of the offending resource), `lineNumber`, and `columnNumber`.

#### Web Authentication
Edge support for the emerging [Web Authentication API](./dev-guide/device/web-authentication.md) using [Windows Hello](http://go.microsoft.com/fwlink/p/?LinkID=624961) biometrics has been updated with the following changes:
- The initial implementation of the experimental Web Authentication API introduced in [EdgeHTML 14](https://blogs.windows.com/msedgedev/2016/08/04/introducing-edgehtml-14/#TVSCzKDkG4jCI5mt.97) (Windows 10 Anniversary Update, build 10240, 7/2016) was exposed through MS- prefixed APIs (the [MSCredentials](https://msdn.microsoft.com/library/mt697639) interface). While these APIs are still available in EdgeHTML 15, they are now deprecated in favor of the non-prefixed, standards-based APIs and behaviors defined in a more [recent snapshot](http://www.w3.org/TR/2016/WD-webauthn-20160928) of the specification, and are likely to continue changing as the spec matures toward standardization.

- The latest Edge implementation is turned off by default and ships behind a flag (type `about:flags` in your address bar to turn on the feature).

- Microsoft Edge does not yet support external credentials like USB keys or Bluetooth devices. The current API is limited to embedded credentials stored in the TPM. A software fallback is used if TPM is not available on the device.

- The currently logged in Windows user account must be configured to support at least a PIN, and preferably face or fingerprint biometrics. This is to ensure that Windows can authenticate the access to the TPM.

- Of the [predefined extensions](http://www.w3.org/TR/webauthn/#extension-predef) described in the spec, Microsoft Edge only supports the [FIDO AppId](http://www.w3.org/TR/webauthn/#extension-appid) (`webauthn_txAuthSimple`) at this time.

- The `timeoutSeconds` option is not currently evaluated

#### WebDriver

EdgeHTML 15 brings a handful of WebDriver updates including support for the silent command line flag and new command endpoints:

Method | URI Template | Command
:----- | :----------  | :--------
POST | /session/{session id}/alert/accept | [Accept Alert](https://w3c.github.io/webdriver/webdriver-spec.html#dfn-accept-alert)
POST | /session/{session id}/alert/dismiss | [Dismiss Alert](https://w3c.github.io/webdriver/webdriver-spec.html#dfn-dismiss-alert)
GET | /session/{session id}/alert/text | [Get Alert Text](https://w3c.github.io/webdriver/webdriver-spec.html#dfn-get-alert-text)
POST | /session/{session id}/alert/text | [Send Alert Text](https://w3c.github.io/webdriver/webdriver-spec.html#dfn-send-alert-text)
POST | /session/{session id}/execute/async | [Execute Async Script](https://w3c.github.io/webdriver/webdriver-spec.html#dfn-execute-async-script)
POST | /session/{session id}/execute/sync | [Execute Script](https://w3c.github.io/webdriver/webdriver-spec.html#dfn-execute-script)
GET | /session/{session id}/window | [Get Window Handle](https://w3c.github.io/webdriver/webdriver-spec.html#get-window-handle)
GET | /session/{session id}/window/handles | [Get Window Handles](https://w3c.github.io/webdriver/webdriver-spec.html#dfn-get-window-handles)

For more info and the status of other WebDriver features, check out [WebDriver commands](./webdriver-commands.md).


## Previous EdgeHTML releases
[EdgeHTML 13 / Windows build 14393 (11/2015)](https://blogs.windows.com/msedgedev/2015/11/16/introducing-edgehtml-13-our-first-platform-update-for-microsoft-edge/#TuusgqsWj8X6pDcD.97)

[EdgeHTML 14 / Windows build 10240 (7/2016)](https://blogs.windows.com/msedgedev/2016/08/04/introducing-edgehtml-14/#6xVDezg4bfyDyIWJ.97)
