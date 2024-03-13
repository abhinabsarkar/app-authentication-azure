# Application Authentication in Azure
[Microsoft identity platform](https://learn.microsoft.com/en-us/entra/identity-platform/v2-overview) allows to build applications that users and customers can sign in to using their Microsoft identities or social accounts. It authorizes access to your own APIs or Microsoft APIs like Microsoft Graph.

![alt txt](/images/about-microsoft-identity-platform.png)

## Application Authentication using MS Entra ID (Azure AD)

## Case: Entra ID secured Web App accessing secured Web API

> OAuth 2.0 authorization code flow

![alt txt](/images/oauth2-code-flow.png)

* Sample code showing above scenario(s)
    * [Secure an ASP.NET Core web API with Microsoft Entra ID](https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-web-api-aspnet-core-protect-api)
    * [ASP.NET Core web app to sign in users and call Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-1-Call-MSGraph/README.md)
    * [ASP.NET Core web app to sign in users and call Web API - ToDoList Web App & ToDoList Web API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-1-MyOrg)

The authentication flow works is explained below:
1. Microsoft Entra ID registered user signs in to the web application using OpenID Connect (OIDC). OpenID Connect (OIDC) extends the OAuth 2.0 authorization protocol for use as an additional authentication protocol. OIDC is used to enable single sign-on (SSO) between your OAuth-enabled applications by using a security token called an ID token.

    ![alt txt](/images/oidc-workflow-uml.png)

2. Apps using the OAuth 2.0 authorization code flow acquire an access_token to include in requests to resources protected by the Microsoft identity platform (typically APIs). Apps can also request new ID and access tokens for previously authenticated entities by using a refresh mechanism.

    ![alt txt](/images/oauth2-codeflow-uml.png)

### See the above flow in action using Postman or Curl

**1. Get the authorization code**  
User logs in to get the authorization code  
Microsoft Entra host: https://login.microsoftonline.com/{tenantId}  
Token Endpoint: `oauth/v2.0/authorize`
```bash
# Use browser to get the oauth authorization code 

https://login.microsoftonline.com/{tenantId}/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&redirect_uri={redirectUri}&response_mode=query&scope={}

# e.g. scope = openid 
# redirectUri = https://localhost
```

The code will look like the image shown below.

![alt txt](/images/authcode.png)

**2. Acquire the ID token, Access token & Refresh token**

Use the authorization code to request the token  
Microsoft Entra host: https://login.microsoftonline.com/{tenantId}  
Token Endpoint: `oauth/v2.0/token`

```bash
curl --location 'https://login.microsoftonline.com/{tenantId}/oauth2/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--form 'grant_type="authorization_code"' \
--form 'client_id="{clientId}"' \
--form 'client_secret="{clientSecret}' \
--form 'code="0.xxxxx"' \
--form 'redirect_uri={redirectUri}'
```

The above request returns ID token, Access Token & Refresh Token

**3. Use the Access token as Bearer token to access the secured API**

Use the Access token to request the secured API (Graph API in this case)  
Microsoft Entra host: https://graph.microsoft.com  
Token Endpoint: `/v1.0/me`

```bash
curl --location 'https://graph.microsoft.com/v1.0/me' \
--header 'Authorization: Bearer {accessToken}'
```

## Case: Entra ID secured Web App accessing secured Web API which calls downstream Web API  on-behalf of the signed-in user 

> OAuth 2.0 On-Behalf-Of user flow

![alt txt](/images/oauth2-code-on-behlaf-of-flow.png)

Sample code showing above concept (the client in the sample is desktop):
* [Sign a user into a Desktop application using Microsoft Identity Platform and call a protected ASP.NET Core Web API, which calls Microsoft Graph on-behalf of the user](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph)

The authentication flow works is explained below:
1. Microsoft Entra ID registered user signs in to the web application using OpenID Connect (OIDC).

    ![alt txt](/images/oidc-workflow-uml.png)

2. Web App calls the ASP.NET Core Web API by using the access token as a bearer token.
3. Web API acquires another access token on-behalf-of the signed-in user.
4. The Web API then uses this new Access token to call Microsoft Graph.

    ![alt txt](/images/oauth-on-behalf-of-flow-uml.png)

## References
* [Microsoft identity platform](https://learn.microsoft.com/en-us/entra/identity-platform/v2-overview)
* [Microsoft Entra authentication endpoints](https://learn.microsoft.com/en-us/entra/identity-platform/authentication-national-cloud#microsoft-entra-authentication-endpoints)
* [Microsoft identity platform code samples](https://learn.microsoft.com/en-us/entra/identity-platform/sample-v2-code?tabs=apptype)
* [OpenID Connect on the Microsoft identity platform](https://learn.microsoft.com/en-us/entra/identity-platform/v2-protocols-oidc)
* [Microsoft identity platform and OAuth 2.0 authorization code flow](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-auth-code-flow)
* [Microsoft identity platform and OAuth 2.0 On-Behalf-Of flow](https://learn.microsoft.com/en-us/entra/identity-platform/v2-oauth2-on-behalf-of-flow
)
* [Overview of the Microsoft Authentication Library (MSAL)](https://learn.microsoft.com/en-us/entra/identity-platform/msal-overview)