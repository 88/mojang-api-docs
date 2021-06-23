# Authenticating a Microsoft account
This article aims to document how to log in to a Microsoft account to authenticate with Minecraft.

**WARNING:** This is not a finished article!

There are two ways to authenticate a Microsoft account - via the email and password or via OAuth 2. Both ways will be outlined in this article.

## Email & Password
**Note:** You will need to enable redirects with whatever request library you're using. You will also need to be able to see the final request URL after redirects.

### Preparing to log in
Before we sign in, we first need to extract two values that we will need in order to sign in. 

Send a `GET` request to `https://login.live.com/oauth20_authorize.srf?client_id=000000004C12AE6F&redirect_uri=https://login.live.com/oauth20_desktop.srf&scope=service::user.auth.xboxlive.com::MBI_SSL&display=touch&response_type=token&locale=en`, then in the page source find the `sFTTag` value:

```
sFTTag:'<input type="hidden" name="PPFT" id="i0327" value="Value we want is here!"/>'
```

In testing, using the regular expression `value="(.+?)"` and extracting the first group is an easy way to extract this value. Save it, as it is needed for the second step.

We also need to get the `urlPost` value from the page source:

```
urlPost:'The value we want is in here!'
```

The regular expression `urlPost:'(.+?)'` works fine for this, just extract the first group. Save this value. 

You may need to modify these regular expressions if, for example, the response text escapes single quotes.

Take note that you **CANNOT** move on to the next step unless you have both of these values.

### Signing in to Microsoft
Now that we have the required values, let's sign into Microsoft. In order to do this, we must send a `POST` request to the URL in the `urlPost` value we saved in the first step.

The POST request must have the body of:
```
login=EMAIL_HERE&loginfmt=EMAIL_HERE&passwd=PASSWORD_HERE&PPFT=SFTTAG_VALUE_HERE
```

Make sure you URL encode the email, password, and sFTTag value. The `Content-Type` header should be set to `application/x-www-form-urlencoded`. Make sure you also enable following redirects in whatever request library you use.

After the request is done following all redirects, check if "accessToken" is present in the new URL, and if the new URL is the same as the `urlPost` URL that we first sent the request to. If both of these things are the case, the login failed. 

If the page source contains `"Sign in to"`, your credentials were wrong. If the page source contains `"Help us protect your account"`, 2-factor authentication is enabled on this account and must be removed or you need to enter your security information to confirm the login.

Now we need to get the tokens that were returned inside this URL. Split the new URL by `#` so that you can get just the URL parameters and their values. You can then split the string again by `&` to get each `param=value` pair, and again to each pair split by `=` to get the parameter name and value. Store these values however you wish.

Some Python example code:
```python
raw_login_data = login_request.url.split("#")[1] # split the url to get the parameters
login_data = dict(item.split("=") for item in raw_login_data.split("&")) # create a dictionary of the parameters
login_data["access_token"] = requests.utils.unquote(login_data["access_token"]) # URL decode the access token
login_data["refresh_token"] = requests.utils.unquote(login_data["refresh_token"]) # URL decode the refresh token
print(login_data) # print the data
```

This code returns the data in a `dict`, like so:
```python
{
   'access_token': 'access token here',
   'token_type': 'bearer',
   'expires_in': '86400',
   'scope': 'service::user.auth.xboxlive.com::MBI_SSL',
   'refresh_token': 'refresh token here',
   'user_id': 'user id here'
}
```

Save the `access_token` value (and possibly the `refresh_token` value if you want!) and we can move on to the next step! Skip the "OAuth 2" section and move straight onto "Signing into Minecraft"!

## OAuth 2
**Note:** Before anything else, you must first get an OAuth 2 Client ID and Client Secret for your MSA. You can do this by clicking [here](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app) to register an Azure application.

### Logging into the MSA
You must do this step in a browser.

Navigate to the following URL: `https://login.live.com/oauth20_authorize.srf?client_id=AZURE_CLIENT_ID_HERE&response_type=code&redirect_uri=REDIRECT_URI_HERE&scope=XboxLive.signin%20offline_access&state=NOT_NEEDED`.

From here, you'll be prompted to enter your MSA login information, and on a successful signin you'll be redirected to the following URL: `https://REDIRECT_URI_HERE?code=CODE_HERE&state=NOT_NEEDED`. The `code` parameter must be extracted as you'll need this later on.

### Get an access token
After you have your code, you need to get your access token. This needs your client secret from earlier. Keep this client secret safe!

Then, send a `POST` request to `https://login.live.com/oauth20_token.srf` with the following POST body and the header `Content-Type: application/x-www-form-urlencoded`:

```
client_id=CLIENT_ID_HERE&client_secret=CLIENT_SECRET_HERE&code=CODE_FROM_PREVIOUS_STEP&grant_type=authorization_code&redirect_uri=REDIRECT_URI_HERE
```

If the request is successful, it will return a sample response such as this:

```json
{
    "token_type": "bearer",
    "expires_in": 86400,
    "scope": "XboxLive.signin",
    "access_token": "ACCESS_TOKEN_HERE",
    "refresh_token": "M.R3_BAY.REFRESH_TOKEN_HERE",
    "user_id": "USER_ID_HERE",
    "foci": "1"
}
```

If you want to refresh an existing `access_token`, you can use the `refresh_token` and send a slightly different request - use the POST body:

```
client_id=CLIENT_ID_HERE&client_secret=CLIENT_SECRET_HERE&refresh_token=REFRESH_TOKEN_HERE&grant_type=refresh_token&redirect_uri=REDIRECT_URI_HERE
```

You will get the same response as shown above if the request is successful. Save the `access_token` and move onto "Signing into Minecraft"!

## Signing into Minecraft
Now that we have signed into Microsoft, we can use the Microsoft access token in order to sign in to Minecraft. These steps are the same regardless of if you use OAuth 2 or plain email and password login.

### Signing into Xbox Live
You now must sign into Xbox Live. In order to do this, you must send a `POST` request to `https://user.auth.xboxlive.com/user/authenticate` with the following body, and the headers `Content-Type: application/json` and `Accept: application/json`:

```json
{
    "Properties": {
        "AuthMethod": "RPS",
        "SiteName": "user.auth.xboxlive.com",
        "RpsTicket": "ACCESS_TOKEN_HERE"
    },
    "RelyingParty": "http://auth.xboxlive.com",
    "TokenType": "JWT"
}
```

If all is successful, you will see a response similar to the below sample response:

```json
{
    "IssueInstant": "2021-02-07T16:43:21.3324153Z",
    "NotAfter": "2021-02-21T16:43:21.3324153Z",
    "Token": "TOKEN_HERE",
    "DisplayClaims": {
      "xui": [
         {
            "uhs": "USER_HASH_HERE"
         }
      ]
    }
}
```

Save the `Token` and the `uhs` values and move onto the next step!

### Getting an XSTS token
This is the final step before we can log in with Minecraft! Send a `POST` request to `https://xsts.auth.xboxlive.com/xsts/authorize` with the following JSON body and the headers `Content-Type: application/json` and `Accept: application/json`:

```json
{
    "Properties": {
        "SandboxId": "RETAIL",
        "UserTokens": [
            "TOKEN_HERE_FROM_PREVIOUS_STEP"
        ]
    },
    "RelyingParty": "rp://api.minecraftservices.com/",
    "TokenType": "JWT"
}
```

You can either get a successful response or a 401 response.

**Successful response:**

```json
{
    "IssueInstant": "2021-02-07T21:41:03.6735105Z",
    "NotAfter": "2021-02-08T13:41:03.6735105Z",
    "Token": "SAVE_THIS_TOKEN",
    "DisplayClaims": {
      "xui": [
         {
            "uhs": "SAME_USER_HASH_AS_BEFORE"
         }
      ]
    }
}
```

**401: Unauthorized**

```json
{
    "Identity": "0",
    "XErr": 2148916238, // can either be 2148916238 (account belongs to someone under 18 and needs to be added to a family) or 2148916233 (account has no Xbox account, you must sign up for one first)
    "Message": "",
    "Redirect": "https://start.ui.xboxlive.com/AddChildToFamily"
}
```

### Getting the bearer token for Minecraft
Now, we can finally get the Minecraft bearer token. Send a `POST` request to `https://api.minecraftservices.com/authentication/login_with_xbox`. The POST body should be similar to:
```json
{
   "identityToken" : "XBL3.0 x=USER_HASH_HERE;XSTS_TOKEN_FROM_PREVIOUS_STEP",
   "ensureLegacyEnabled" : true
}
```

You must send this request with the `Content-Type: application/json` header.

You will get a response similar to this:

```json
{
  "username" : "830491e6-50e9-e9ed-6e8a-7041f4fef585", // Xbox account username (?)
  "roles" : [ ], // i suppose this will have something in it if the account has a copy of Minecraft
  "access_token" : "eyJhbGciOiJIUzI1NiJ9.eyJ4dWlkIjoiMjUzMzI3NDgwNTgyOTU2OCIsInN1YiI6IjgzMDQ5MWU2LTUwZTktZTllZC02ZThhLTcwNDFmNGZlZjU4NSIsIm5iZiI6MTYwNjg0NTE0Niwicm9sZXMiOltdLCJpc3MiOiJhdXRoZW50aWNhdGlvbiIsImV4cCI6MTYwNjkzMTU0NiwiaWF0IjoxNjA2ODQ1MTQ2LCJ5dWlkIjoiNTRmYzUwYzFiMWJjMTlkZDEwOWZiZWZmOWQxMWJmNDEifQ.-7H1jT3i54gmbqQcUBdHArOn-Jr8eZVAyPDiOr5q_XY", // access token
  "token_type" : "Bearer", // don't think there are other types
  "expires_in" : 86400 // number of seconds until token expires. MSA tokens last 24 hours. yes, really, 24 hours!
}
```

The `access_token` is what you will now use to authenticate yourself to other API endpoints.

See the "A peek into Bearer tokens" article for more information about what's inside the `access_token`.
