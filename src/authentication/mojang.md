# Authenticating a Mojang account
In order to use any of the endpoints on Mojang's API that require authentication, we of course need to be authenticated. This article goes through how to authenticate a user via Mojang's `authserver`, codename Yggdrasil, to get an authorization token we can use.

Keep in mind, this endpoint is not on the main Mojang API domain, instead using `authserver.mojang.com`.

### Request
- **Method:** `POST`
- **Endpoint:** `/authenticate`
- **Full URL:** `https://authserver.mojang.com/authenticate`
- **Headers:**
    - `Content-Type: application/json`

The POST body should fit this format:

```json
{
  "agent" : {
    "name" : "Minecraft", // identifying which game is connecting, "Scrolls" returns Scrolls profile info
    "version" : 1 // version of the agent (OPTIONAL)
  }, // you don't even need this! "agent" : "minecraft" works fine too.
  "username" : "testuser@domain.tld", // username (legacy) or email address
  "password" : "CoolPassw0rd!34^", // password
  "clientToken" : "Mojang-API-Client", // client token used to identify yourself (OPTIONAL)
  "requestUser" : "true" // request a response back containing user information (OPTIONAL)
}
```

**Note:** If `clientToken` is `null` or not provided in the POST body, Yggdrasil will generate a random UUIDv4 as the `clientToken`.

Interestingly enough, Mojang only cares about the first 72 characters of a user's password. You don't need to supply any more characters, but if you do and if you get any more characters wrong, it'll still let you in.

### Response
**200: OK**

We have successfully authenticated. Below is a sample response of what you would recieve when you are successfully authenticated:

```json
{
  "user" : { // Mojang user info (only when requestUser is true)
    "properties" : [ // user properties (MAY NOT BE RETURNED.)
      {
        "name" : "preferredLanguage", // which language system emails will be sent in
        "value" : "en-us" // IETF language tag
      },
      {
        "name" : "registrationCountry", // where the account was first registered
        "value" : "US" // two-letter country code
      }
    ],
    "username" : "newname34234", // Mojang account email or username (legacy)
    "id" : "2ea6d02ea02ea6e2acf579df2e2eb15f" // Mojang account identifier (userId value)
  },
  "accessToken" : "eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIyZWE2ZDAyZWEwMmVhNmUyYWNmNTc5ZGYyZTJlYjE1ZiIsInlnZ3QiOiIyZWUzMmRiYjJlMzM0ODJlMmVkMWVhMmQ2MzYyMjlhOCIsInNwciI6IjMxZTBjY2JlZjVmYTRlYjk4ODU5MmYzMDUxNmY2NWZlIiwiaXNzIjoiWWdnZHJhc2lsLUF1dGgiLCJleHAiOjE2MDcwNjIyMjUsImlhdCI6MTYwNjg4OTQyNX0.GLSOzNO5zbb0aU0emCHfMmEke1kGzfRsk7mwrrBrhbs", // Bearer token
  "clientToken" : "Mojang-API-Client", // client token we identified ourselves with
  "availableProfiles" : [ // array of available profiles (one Mojang account can have multiple Minecraft profiles on it)
    {
      "legacy" : true, // if the account is not legacy, this won't show up
      "name" : "newname34234", // username of this profile
      "id" : "31e0ccbef5fa4eb988592f30516f65fe" // uuid of this profile
    }
  ],
  "selectedProfile" : { // profile info on the currently selected profile
    "legacy" : true, // if the account is not legacy, this won't show up
    "name" : "newname34234", // username of current profile
    "id" : "31e0ccbef5fa4eb988592f30516f65fe" // uuid of current profile
  }
}
```

**400: Bad Request**

This error may be encountered when you have supplied an invalid JSON body.

```json
{
  "error" : "JsonMappingException",
  "errorMessage" : "Unexpected character ('/' (code 47)): maybe a (non-standard) comment? (not recognized as one since Feature 'ALLOW_COMMENTS' not enabled for parser)\n at [Source: (org.eclipse.jetty.server.HttpInputOverHTTP); line: 3, column: 14] (through reference chain: com.mojang.yggdrasil.auth.dataaccess.memcached.throttling.captcha.CaptchaCredentials[\"agent\"])"
}
```

**403: Forbidden**

There can be numerous reasons why you received this error. A few are listed below, with their error messages:

```json
// provided invalid credentials OR you are ratelimited
{
  "error" : "ForbiddenOperationException",
  "errorMessage" : "Invalid credentials. Invalid username or password."
}

// trying to log in to an unmigrated / legacy account which has already been migrated
{
  "error" : "ForbiddenOperationException",
  "errorMessage" : "Invalid credentials. Account migrated, use email as username.",
  "cause" : "UserMigratedException"
}

// did not include the "username" value in POST body
{
  "error" : "ForbiddenOperationException",
  "errorMessage" : "Forbidden"
}
```

**405: Method Not Allowed**

If you are sending any request other than a `POST` request, this error will appear.

```json
{
  "error" : "Method Not Allowed",
  "errorMessage" : "The method specified in the request is not allowed for the resource identified by the request URI"
}
```

**415: Unsupported Media Type**

When encountering this error, you have most likely neglected to add the `Content-Type: application/json` header to your request.

```json
{
  "error" : "Unsupported Media Type",
  "errorMessage" : "The server is refusing to service the request because the entity of the request is in a format not supported by the requested resource for the requested method"
}
```

