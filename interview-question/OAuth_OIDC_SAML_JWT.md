Oauth 2.0 
==============
# Quick Intro
https://aaronparecki.com/oauth-2-simplified/

# PKCE
https://oauth.net/2/pkce/

# Best Practice
https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14

# Framework
https://tools.ietf.org/html/rfc6749#section-1.1

# [Different Flow](https://oauth.net/2/grant-types/)
Grant Type
* Authorization Code
    * used by confidential and public clients to exchange an authorization code for an access token.
* Client Credentials
    * used when applications request an access token to access their own resources, not on behalf of a user.
* Device Code
    * used by browserless or input-constrained devices in the device flow to exchange a previously obtained device code for an access token.
    * While the device is waiting for the user to complete the authorization flow on their own computer or phone, the device meanwhile begins polling the token endpoint to request an access token.
* Refresh Token
    * s used by clients to exchange a refresh token for an access token when the access token has expired.

Legacy
* Implicit Flow
    * previously recommended for native apps and JavaScript apps where the access token was returned immediately without an extra authorization code exchange ste
    * Public clients such as **native apps and JavaScript apps** should now use the authorization code flow with the PKCE (Proof Key for Code Exchange) extension instead.
        * Native app generate code_verifier first.  hash it (SHA 256 and called code challenge) and pass it in the authorization request and same as Authorization code. But in the token exchange, pass the code_verifier again and authorization server would then compare it
* Password Grant
    *  exchange a user's credentials for an access token
    * the client application has to collect the user's password and send it to the authorization server, it is not recommended that this grant be used at all anymore.
    
 
# [Redirect URI Options](https://www.oauth.com/oauth2-servers/redirect-uris/)
1. private use URI
2. claimed  https
3. loopback device    


For Native client app, don't support embed user agent. Use in-app web browser pattern (RFC 8525)


# OpenId Connect integration (add Identity service to oauth 2)
--> Hybrid Flow. Get Identity Token and Authorization Token together

Metadata Endpoint of Auth Server


# SAML 
is a little old

Saml bearer assertion type to swap with oauth token

# JWT 
To be learned 
https://app.pluralsight.com/library/courses/oauth2-json-web-tokens-openid-connect-introduction/table-of-contents