# Integration steps & requirements

In order to be able to authenticate, issue and verify credentials we’ll need to do some setup to make it work. We’ll guide you through the following steps:

1. Setup an OIDC client, i.e. the component that will interact with the OIDC provider (ValidatedId) so as to delegate the authentication
2. Create your entity DID, i.e. the decentralized identifier of your institution
3. Accessing the API (Authentication)
4. Define the contents of your Verifiable Credentials
5. Check the API Reference to integrate your website with our API in order to issue, request and verify VCs.
6. Get the VIDchain Wallet


## Set up your OIDC client 

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



## Create your entity DID

In the near future, we will create a website to auto-enroll in our API, but for the moment, we’ll do it for you. 


## Accessing the API: API Bearer Token Authentication

In order to authenticate with the API, we’ll be using **Bearer Token Http authentication scheme**. You will request an access token that you will include as a Bearer Token in all the requests to the API protected resources.

### Create an assertion to request the access token
To get the access toke, first create an `assertion` JWT token and encode it in `base64`:

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
    "nonce": "z-0427dc2516d0" (random nonce),
    "apiKey": <the api-key we have sent to you>,
    "callbackUrl: "https://<entity backend url>/<callback path>",
    "image": "iVBORw0KGgoAAgAAADwAAAANCA...", //optional
    "icon": "iVBORw0KGgoAAgAAADwAAAANCA..." //optional
  }
  ```

  * **iss**: this field must contain the client ID provided to identify the Entity on the VIDchain API.
  * **nonce**: random number.
  * **api-key**: API key we have provided you.
  * **callbackUrl** (optional): is the URL where the VIDchain API should be able to redirect when finalising an async process like a Presentation Request. Notice this is not the OIDC callback but the entity backend endpoint where the VIDChain API will send back the endpoint where to retrieve the VP.

> **NOTE**: JWTs must be signed (JWS) when using Prod API. **When using Test API you just need to Base64-encode the payload**.

### Obtain a valid access token
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

The **assertion** is the JWT you created on the first step.

As a **response** you will receive:

``` javascript
{
  "accessToken": "eyJhbGciOiJFUzI1NksiLCJ0eXAiOiJKV1QiLCJraWQiOiJ2aWRjaGFpbi1hcGkifQ.eyJzdWIiOiJFTlRJVFktTkFNRSIsImRpZCI6ImRpZDp2aWQ6MHg3OTc0ZGU2NTY4OEFiNTU0QWZENDk1NWMxMkYzQzk0MjdmM0E4QzFBIiwibm9uY2UiOiJ6LTA0MjdkYzI1MTZkMCIsImlhdCI6MTU5ODAyMjM0MSwiZXhwIjoxNjAwNjE0MzQxLCJhdWQiOiJ2aWRjaGFpbi1hcGkifQ.CbsJxbeMmZj8lS8k_-QH4zPLjvYcWjDDpZ7vrOGFq2R30ZSH4bCoZBz2Ra4LXYMkYjH_jPBikso667baudsI9w",
  "tokenType": "Bearer",
  "expiresIn": 1600614341,
  "issuedAt": 1598022341
}
```
* The `accessToken` is the Bearer token you need to include in further calls to protected VIDchain API endpoints.

### Client-id and entity session key registration (Prod API only)

To be added soon.

In order to register the entity session, an API endpoint will be created so that entities can auto-register their session keys linked to their chosen client-id.


## Define the contents of the Verifiable Credentials

Verifiable Credentials contain details about the user to which they are issued. Verifiable Credentials typically contain a number of key-value pairs that describe attributes, or claims, about an individual. While this will be automated shortly, we are creating the credentials schemes manually for now. Then, you will need to prepare the list of items to include in your credentials.


## Integrate your web application using our API reference

Get ready to integrate VIDChain API in your web application by checking our OpenAPI specification in the following URL: [https://api.vidchain.net/api/v1/api-docs/](https://api.vidchain.net/api/v1/api-docs/)

The following endpoints are available to interact with our API:

![openapi-services](_media/openapi-services.jpg)


## Get the VIDwallet app

As a user, in order to create keys, receive credentials, use them and be able to complete the tutorial you will need a wallet.

### Download VIDwallet

You can download the VIDchain reference wallet here: 

- [Google APK Version for Android](https://drive.google.com/file/d/1En7_nhd0ANb3ZZe3DVaMPnmqlRfK8zYC/view?usp=sharing)


Once installed, the first time you open the app you’ll be asked to define a PIN code or to authenticate using your biometrics (used to encrypt the keys). As soon as you open the wallet, your keys will be created.


![filename](images/image2.png "image_tooltip")

The application has four views:

*   **Credentials**: list of all the credentials you own and store in your wallet.
*   **Login**: where you can scan a QR code presented by  a web page the user asks to authenticate in. 
*   **Notifications**: list of all the notifications you receive from a credentials provider, like .
*   **Settings**



