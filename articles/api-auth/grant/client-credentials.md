---
description: Describes how to call APIs from server processes using the Client Credentials Grant.
---

# Calling APIs from a Service

The OAuth 2.0 grant that machine-to-machine interfaces utilize in order to access an API, is the **Client Credentials Grant**.

## Overview

With **Client Credentials Grant** (defined in [RFC 6749, section 4.4](https://tools.ietf.org/html/rfc6749#section-4.4)) a Client can directly request an `access_token` to the Authorization Server by using its Client Credentials (a Client Id and a Client Secret). Instead of identifying a Resource Owner, this token will represent the Client itself.

![Client Credentials Grant Flow](/media/articles/api-auth/client-credentials-grant.png)

 1. The Client authenticates with the Authorization Server using its Client Id and Client Secret
 2. The Authorization Server validates this information and returns an `access_token`
 3. The Client can use the `access_token` to call the Resource Server on behalf of itself

This flow is not redirect based but is an API call made by the Client to the Authorization Server. And finally the resulting access token can be used by the Client to call the Resource Server.

## Use Case

 - Allow the Client to make calls to the Resource Server on its own behalf (machine to machine)
 - APIs and services that are not user centric

## Read more

[How to set up a Client Credentials Grant using the Dashboard](/api-auth/config/using-the-auth0-dashboard)

[How to set up a Client Credentials Grant using the Management API](/api-auth/config/using-the-management-api)

[How to execute a Client Credentials Grant](/api-auth/config/asking-for-access-tokens)

[How to change the scopes and add custom claims to the tokens using Hooks](/api-auth/tutorials/client-credentials/customize-with-hooks).
