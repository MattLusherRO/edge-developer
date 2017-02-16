---
description: Learn how the Web Authentication API can be used to enable web applications to use Windows Hello biometrics for user authentication.
title: Dev guide - Web authentication
author: erikadoyle
ms.author: edoyle
ms.date: 02/10/2017
ms.topic: article
ms.prod: microsoft-edge
keywords: edge, web development, html, css, javascript, developer
---

# Web authentication and Windows Hello

The Web Authentication (formerly *FIDO 2.0* ) API in Microsoft Edge enables web applications to use [Windows Hello](http://go.microsoft.com/fwlink/p/?LinkID=624961) biometrics for user authentication so that you and your users can avoid the hassles and risks of password management, including password guessing, phishing, and keylogging attacks. The current Microsoft Edge implementation is based on a [recent draft](http://www.w3.org/TR/2016/WD-webauthn-20160928) of the Web Authentication specification and will continue to change as the spec evolves.

The Web Authentication API is an emerging standard put forth by the [FIDO Alliance](https://fidoalliance.org/) and the [W3C](http://www.w3.org/Webauthn/) with the aim of providing an interoperable way of doing web authentication using Windows Hello and other biometric devices across browsers. The Web Authentication specification defines two authentication scenarios: password-less and two factor. In the password-less case, you can login to a website with Windows Hello using a fingerprint reader or your camera to recognize your face. In the two factor case, you login using a username and password, but Windows Hello is used as a second factor check to make the overall authentication stronger.

Using Web Authentication combined with Windows Hello, the server sends down a plain text challenge to the browser. Once Microsoft Edge is able to verify the user through Windows Hello, the system will sign the challenge with a private key previously provisioned for this user and send the signature back to the server. If the server can validate the signature using the public key it has for that user and verify the challenge is correct, it can authenticate the user securely. With [asymmetric cryptography](https://en.wikipedia.org/wiki/Public-key_cryptography) such as this, the public key is meaningless on its own and the private key is never shared. Furthermore, the private key can never be moved off a modern system with TPM-enabled hardware.

There are two basic steps to using the Web Authentication API:

**1. Register your user with `makeCredential`**

**2. Authenticate your user with `getAssertion`**

## Register your user

Acting as an *identity provider*, you will first need to create a Web Authentication credential for your user with the navigator.authentication.**makeCredential** method:

```
    // Display authentication UI to the user
	navigator.authentication.makeCredential(userAccountInformation, cryptoParams, challenge).
        then(function (credential) {
        // Register new credential info on server for future verifications
	}).catch(function (err) {
	    // No acceptable authenticator or user refused consent. Handle appropriately.
	    alert(err);
	});
```

The makeCredential method takes the following parameters:

 - **user account information**
Windows Hello will use this supplied info later when it needs to ask the user which credential (e.g., which user account) to login with.
```
    var userAccountInformation = {
        rpDisplayName: "Contoso",
        displayName: "User Name Here",
        id: "1234567890"
    };
```

 - **crypto parameters**
The crypto parameters specify the desired properties of the credential to be created.
```
    var cryptoParams = [
        {
            type: "ScopedCred",
            algorithm: "RSASSA-PKCS1-v1_5",
        }
    ];
```

 - **attestation challenge**
The challenge is used for generating the attestation statement for the new credential.

```
    var challenge = new Uint8Array([0x9B, 0x49, 0xBE, 0x63, 0xC9, 0xAD, 0x1F, 0xEF, 0xDE, 0x67, 0x85, 0xF9]);

```

When you use the makeCredential method, Microsoft Edge will first ask Windows Hello to use face or fingerprint identification to verify that the user is the same user as the one logged into the Windows account. Once this step is completed, Microsoft Passport will generate a public/private key pair and store the private key in the Trusted Platform Module (TPM), the dedicated crypto processor hardware used to store credentials. If the user doesn’t have a TPM enabled device, these keys will be stored in software. These credentials are created per origin, per Windows account, and will not be roamed because they are tied to the device. This means that you’ll need to make sure the user registers to use Windows Hello for every device they use.


## Authenticate your user

Once the credential is created on the client, the next time the user attempts to log into the site you can offer to sign them in using Windows Hello instead of a password with a call to navigator.authentication.**getAssertion**:

```
navigator.authentication.getAssertion(challenge)
    .then(function (assertion) {
    // Send assertion to the server for verification
}).catch(function (err) {
    // No acceptable credential or user refused consent. Handle appropriately. 
});
```

The getAssertion method takes the *challenge* as its only required parameter. The challenge is the randomly generated quantity that the server will send down to a client to sign with the user's private key.

When the getAssertion call is made, Microsoft Edge will show the Windows Hello prompt, which will verify the identity of the user using biometrics. After the user is verified, the challenge will be signed within the TPM and the promise will return with an assertion object that contains the signature and other metadata for you to send to the server.

Once you receive the assertion on the server, you will need to validate the signature to complete the user authentication process. 

## Notes on Microsoft Edge
- The initial implementation of the experimental Web Authentication API introduced in [EdgeHTML 14](https://blogs.windows.com/msedgedev/2016/08/04/introducing-edgehtml-14/#TVSCzKDkG4jCI5mt.97) (Windows 10 Anniversary Update, build 10240, 7/2016) was exposed through MS- prefixed APIs (the [MSCredentials](https://msdn.microsoft.com/library/mt697639) interface). While these APIs are still available in EdgeHTML 15, they are now deprecated in favor of the non-prefixed, standards-based APIs and behaviors defined in a more [recent snapshot](http://www.w3.org/TR/2016/WD-webauthn-20160928) of the specification, and are likely to continue changing as the spec matures toward standardization.

- The latest Edge implementation is turned off by default and ships behind a flag (type `about:flags` in your address bar to turn on the feature).

- Microsoft Edge does not yet support external credentials like USB keys or Bluetooth devices. The current API is limited to embedded credentials stored in the TPM.

- The currently logged in Windows user account must be configured to support at least a PIN, and preferably face or fingerprint biometrics. This is to ensure that Windows can authenticate the access to the TPM.

### IDL support
Here is a snapshot of Web Authentication API support as of EdgeHTML 15 (Windows 10 Creators Update, build #####, MM/2017).
```
    interface WebAuthentication {
        Promise<ScopedCredentialInfo> makeCredential(
            Account accountInformation,
            sequence<ScopedCredentialParameters> cryptoParameters,
            BufferSource attestationChallenge,
            optional ScopedCredentialOptions options
        );
        Promise<WebAuthnAssertion> getAssertion(
            BufferSource assertionChallenge,
            optional AssertionOptions options
        );
    };

    interface ScopedCredentialInfo {
        readonly attribute ScopedCredential credential;
        readonly attribute CryptoKey publicKey;
    };

    dictionary Account {
        required DOMString rpDisplayName;
        required DOMString displayName;
        required DOMString id;
        DOMString name;
        DOMString imageURL;
    };

    dictionary ScopedCredentialParameters {
        required ScopedCredentialType type;
        required AlgorithmIdentifier algorithm;
    };

    dictionary ScopedCredentialOptions {
        unsigned long timeoutSeconds;
        USVString rpId;
        sequence<ScopedCredentialDescriptor> excludeList;        WebAuthnExtensions extensions;
    };

    interface WebAuthnAssertion {
        readonly attribute ScopedCredential credential;
        readonly attribute ArrayBuffer clientData;
        readonly attribute ArrayBuffer authenticatorData;
        readonly attribute ArrayBuffer signature;
    };

    dictionary AssertionOptions {
        unsigned long timeoutSeconds;
        USVString rpId;
        sequence <ScopedCredentialDescriptor> allowList;
        WebAuthnExtensions extensions;
    };

    dictionary WebAuthnExtensions {
    };

    dictionary ClientData {
        required DOMString           challenge;
        required DOMString           origin;
        required DOMString           rpId;
        required AlgorithmIdentifier hashAlg;
        DOMString                    tokenBinding;
        WebAuthnExtensions           extensions;
    };

    enum ScopedCredentialType {
        "ScopedCred"
    };

    interface ScopedCredential {
        readonly attribute ScopedCredentialType type;
        readonly attribute ArrayBuffer id;
    };

    dictionary ScopedCredentialDescriptor {
        required ScopedCredentialType type;
        required BufferSource id;
        sequence<Transport> transports;
    };

    enum Transport {
        "usb",
        "nfc",
        "ble"
    };
```

## Demos

[Client and server code samples](https://github.com/adrianba/fido-snippets/)

[Windows Hello Test Drive demo](https://testdrive-fido.azurewebsites.net/)

## Specification

[Web Authentication: A Web API for accessing scoped credentials](http://w3c.github.io/webauthn/)
