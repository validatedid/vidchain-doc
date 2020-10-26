
# What is VIDchain?

VIDchain is an SSI service.

It is composed of diferent building blocks:
 - **VIDwallet**, an app for users to hold W3C Verifiable Credentials (VC) and generate and manage user DID.
 - **VIDauth**, an OpenID provider that is able to perform DID authentication.
 - **VIDcredentials**, a service that allows VC management (request and sending credentials).

![vidchain-components](_media/vidchain-components.jpg)


# This guide
This document provides information on how to use the VIDchain API to create and request credentials and how to register as a client to use VIDchain OpenID provider.

It is divided in two parts: the requirements and the tutorial. The former provides an overview of the items needed in order to be ready to start the tutorial. Therefore, please read the requirements first so as to get ready for the integration.

- [Integration steps & requirements](/requirements.md)
  - [Set up your OIDC client](/requirements.md#set-up-your-oidc-client)
  - [Create your entity DID](/requirements.md#create-your-entity-did)
  - [Accessing the API: API Bearer Token Authentication](/requirements.md#client-id-and-entity-session-key-registration-prod-api-only)
  - [Define the contents of the Verifiable Credentials](/requirements.md#define-the-contents-of-the-verifiable-credentials)
  - [Integrate your web application using our API reference](/requirements.md#integrate-your-web-application-using-our-api-reference)
  - [Get the VIDwallet app](/requirements.md#get-the-vidwallet-app)
- [Tutorial](/tutorial.md)
  - [OIDC flow for DID Auth](/tutorial.md#oidc-flow-for-did-auth) 
  - [Issue Credentials](/tutorial.md#issue-credentials)
  - [Request and Verify credentials](/tutorial.md#request-and-verify-credentials)
  - [Present and Verify credentials](/tutorial.md#present-and-verify-credentials)




