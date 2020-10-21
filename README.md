
# 1. This guide
This document provides information on how to use the VIDchain API to create and request credentials and how to register as a client to use VIDchain OpenID provider.

It is divided in two parts: the requirements and the tutorial. The former provides an overview of the items needed in order to be ready to start the tutorial. Therefore, please read the requirements first so as to get ready for the integration.

- [1. This guide](#1-this-guide)
- [2. What is VIDchain?](#2-what-is-vidchain)
- [3. Integration steps & requirements](#3-integration-steps--requirements)
  - [3.1. Set up your OIDC client](#31-set-up-your-oidc-client)
  - [3.2. Create your entity DID](#32-create-your-entity-did)
  - [3.3. Accessing the API: API Bearer Token Authentication](#33-accessing-the-api-api-bearer-token-authentication)
    - [3.3.1. Create an assertion to request the access token](#331-create-an-assertion-to-request-the-access-token)
    - [3.3.2. Obtain a valid access token](#332-obtain-a-valid-access-token)
    - [3.3.3. Client-id and entity session key registration (Prod API only)](#333-client-id-and-entity-session-key-registration-prod-api-only)
  - [3.4. Define the contents of the Verifiable Credentials](#34-define-the-contents-of-the-verifiable-credentials)
  - [3.5. Integrate your web application using our API reference](#35-integrate-your-web-application-using-our-api-reference)
  - [3.6. Get the VIDwallet app](#36-get-the-vidwallet-app)
    - [3.6.1. Download VIDwallet](#361-download-vidwallet)
  
  
# 2. What is VIDchain?

VIDchain is an SSI service.

It is composed of diferent building blocks:
 - **VIDwallet**, an app for users to hold W3C Verifiable Credentials (VC) and generate and manage user DID.
 - **VIDauth**, an OpenID provider that is able to perform DID authentication.
 - **VIDcredentials**, a service that allows VC management (request and sending credentials).

![vidchain-components](_media/vidchain-components.jpg)





# 3. Integration steps & requirements

In order to be able to authenticate, issue and verify credentials we’ll need to do some setup to make it work. We’ll guide you through the following steps:

1. Setup an OIDC client, i.e. the component that will interact with the OIDC provider (ValidatedId) so as to delegate the authentication
2. Create your entity DID, i.e. the decentralized identifier of your institution
3. Accessing the API (Authentication)
4. Define the contents of your Verifiable Credentials
5. Check the API Reference to integrate your website with our API in order to issue, request and verify VCs.
6. Get the VIDchain Wallet


## 3.1. Set up your OIDC client 

We support OIDC Authorization Code Flow with or without PKCE. We’ll need the following information to register your OIDC client:

*   **Client-id** : a string to identify your client when making calls to the OIDC provider
*   **Secret**: to authenticate your client’s calls to OIDC Provider.
*   **Callback URL**: the URL where the OIDC Provider should send the user after the authentication flow is done.
*   (optional) **name**
*   (optional) **logo** : An image that will appear when user is logging in 

?> Provide this information to our team and we will assist you creating your OIDC client.

An example of OpenID client creation using Typescript and [JSO client](https://www.npmjs.com/package/jso):


``` javascript
import { JSO, Popup } from "jso";

const nonce = utils.randomString(24);
const state = utils.randomString(24);

let configFile = {
  client_id: config.CLIENT_ID,
  client_secret: config.CLIENT_SECRET,
  token: config.IDENTITY_PROVIDER + "/oauth2/token",
  authorization: config.IDENTITY_PROVIDER + "/oauth2/auth",
  redirect_uri: config.REDIRECT_CALLBACK,
  scopes: {
    request: ["openid", "offline"],
    require: ["openid", "offline"],
  },
  response_type: "code",
  debug: true,
};

this.client = new JSO(configFile);
```



## 3.2. Create your entity DID

In the near future, we will create a website to auto-enroll in our API, but for the moment, we’ll do it for you. 


## 3.3. Accessing the API: API Bearer Token Authentication

In order to authenticate with the API, we’ll be using **Bearer Token Http authentication scheme**. You will request an access token that you will include as a Bearer Token in all the requests to the API protected resources.

### 3.3.1. Create an assertion to request the access token
To get the access toke, create an assertion and encode it in base64:

``` javascript
// Sample assertion 
{
	"iss": "ENTITY-NAME",
	"aud": "vidchain-api",
	"nonce": "z-0427dc2516d0",
	"apiKey": "2fb8b85c-ebe8-4a27-8718-ebb778ccf285",
    "callbackUrl": "http://example.info",
    "image": "iVBORw0KGgoAAgAAADwAAAANCA...",
    "icon": "iVBORw0KGgoAAgAAADwAAAANCA..."
}

// Assertion base64-encoded:
ewoJImlzcyI6ICJFTlRJVFktTkFNRSIsCgkiYXVkIjogInZpZGNoYWluLWFwaSIsCgkibm9uY2UiOiAiei0wNDI3ZGMyNTE2ZDAiLAoJImFwaUtleSI6ICIyZmI4Yjg1Yy1lYmU4LTRhMjctODcxOC1lYmI3NzhjY2YyODUiLAogICAgImNhbGxiYWNrVXJsIjogImh0dHA6Ly9leGFtcGxlLmluZm8iLAogICAgImltYWdlIjogImlWQk9SdzBLR2dvQUFnQUFBRHdBQUFBTkNBLi4uIiwKICAgICJpY29uIjogImlWQk9SdzBLR2dvQUFnQUFBRHdBQUFBTkNBLi4uIgp9
```

### 3.3.2. Obtain a valid access token
To get the access token you will have to make a `POST` request to `/api/v1/sessions` with the following payload:

``` javascript
{
  "grantType": "urn:ietf:params:oauth:grant-type:jwt-bearer",
  "assertion": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
  "scope": "vidchain profile entity"
}
```

The **grantType** just set it as provided in the example above.

The **scope** can have different values depending on if you are accessing the Prod or the test API:



*   For prod API (api.vidchain.net) use `vidchain profile entity`,
*   For the TEST (dev.vidchain.net) use `vidchain profile test entity`


The **assertion** is a JWT with the following content:

* Header (**only used in Prod API**):
  ``` javascript
  {
    "alg": "ES256K",
    "typ": JWT
  }
  ```

* Payload :
  ``` javascript
  {
    "iss": <client-id>,
    "aud": "vidchain-api",
    "nonce": "z-0427dc2516d0" (random nonce)
    "callbackUrl: "https://<entity backend url>/<callback path>"
  }
  ```

  * **iss**: this field must contain the client ID provided to identify the Entity on the VIDchain API

  * **callbackUrl** (optional): is the URL where the VIDchain API should be able to redirect when finalising an async process like a Presentation Request. Notice this is not the OIDC callback but the entity backend endpoint where the VIDChain API will send back the endpoint where to retrieve the VP.


**NOTE**: JWTs must be signed (JWS) when using Prod API. **When using Test API you just need to Base64-encode the payload**.


### 3.3.3. Client-id and entity session key registration (Prod API only)

To be added soon.

In order to register the entity session, an API endpoint will be created so that entities can auto-register their session keys linked to their chosen client-id.


## 3.4. Define the contents of the Verifiable Credentials

Verifiable Credentials contain details about the user to which they are issued. Verifiable Credentials typically contain a number of key-value pairs that describe attributes, or claims, about an individual. While this will be automated shortly, we are creating the credentials schemes manually for now. Then, you will need to prepare the list of items to include in your credentials.


## 3.5. Integrate your web application using our API reference

Get ready to integrate VIDChain API in your web application by checking our OpenAPI specification in the following URL: [https://api.vidchain.net/api/v1/api-docs/](https://api.vidchain.net/api/v1/api-docs/)

The following endpoints are available to interact with our API:

![openapi-services](_media/openapi-services.jpg)


## 3.6. Get the VIDwallet app

As a user, in order to create keys, receive credentials, use them and be able to complete the tutorial you will need a wallet.

### 3.6.1. Download VIDwallet

You can download the VIDchain reference wallet here: 

[https://drive.google.com/file/d/1lDOE8EaaAc75QULjYmwezTaEHnz5whp-/view?usp=sharing](https://drive.google.com/file/d/1lDOE8EaaAc75QULjYmwezTaEHnz5whp-/view?usp=sharing)

Once installed, the first time you open the app you’ll be asked to define a PIN code or to authenticate using your biometrics (used to encrypt the keys). As soon as you open the wallet, your keys will be created.


![filename](images/image2.png "image_tooltip")

The application has four views:

*   **Profile**: where you can check your user did.
*   **Login**: where you can scan a QR code presented by  a web page the user asks to authenticate in. 
*   **Credentials**: list of all the credentials you own and store in your wallet.
*   **Notifications**: list of all the notifications from a credentials provider.
