# Change your account's username
This endpoint allows a user with a Minecraft profile to change their account's username.

This endpoint has a ratelimit of 3 requests per account and per IP.

### Request
- **Method:** `PUT`
- **Endpoint:** `/minecraft/profile/name/:username`
- **Full URL:** `https://api.minecraftservices.com/minecraft/profile/name/:username`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`

The only URL parameter is `username`, your desired username.

### Response
**200: OK**

You have successfully changed your account's username!

```json
// just returns normal user profile response
{
  "id" : "31e0ccbef5fa4eb988592f30516f65fe",
  "name" : "newname34234",
  "skins" : [ {
    "id" : "c97f6fea-6ca8-4bf0-bdae-b7cf91fb089c",
    "state" : "ACTIVE",
    "url" : "http://textures.minecraft.net/texture/3b60a1f6d562f52aaebbf1434f1de147933a3affe0e764fa49ea057536623cd3",
    "variant" : "SLIM"
  } ],
  "capes" : [ ]
}
```

**400: Bad Request**

This status code is returned when an error occurred when changing your username. Below is a list of the possible errors:

```json
// trying to change to a name of invalid length
{
  "path" : "/minecraft/profile/name/1",
  "errorType" : "CONSTRAINT_VIOLATION",
  "error" : "CONSTRAINT_VIOLATION",
  "errorMessage" : "changeProfileName.profileName: Invalid profile name, changeProfileName.profileName: size must be between 3 and 16",
  "developerMessage" : "changeProfileName.profileName: Invalid profile name, changeProfileName.profileName: size must be between 3 and 16"
}

// trying to change to a name with invalid characters
{
  "path" : "/minecraft/profile/name/%%%%%",
  "errorType" : "CONSTRAINT_VIOLATION",
  "error" : "CONSTRAINT_VIOLATION",
  "errorMessage" : "changeProfileName.profileName: Invalid profile name",
  "developerMessage" : "changeProfileName.profileName: Invalid profile name"
}
```

If interested, invalid length validation takes priority over invalid name validation, it seems.

**401: Unauthorized**

You have not provided a valid JWT / auth token, or you have neglected to provide the `Authorization` header at all.

```json
{
  "path" : "/minecraft/profile/name/:username",
  "errorType" : "UnauthorizedOperationException",
  "error" : "UnauthorizedOperationException",
  "errorMessage" : "Unauthorized",
  "developerMessage" : "Unauthorized"
}
```

**403: Forbidden**

If this error occurs, you are either trying to change the username of an account that has already changed its username in the past 30 days, **OR** you are trying to change the username of your account to a name that has already been taken or is still on cooldown. 

This error can also occur if you are trying to change an account's name that has not had its security questions answered, from what I have tested.

```json
{
  "path": "/minecraft/profile/name/:username",
  "errorType": "FORBIDDEN",
  "error": "FORBIDDEN",
  "errorMessage": "Could not change name for profile",
  "developerMessage": "Could not change name for profile",
  "details": {
    "status": "DUPLICATE"
  }
}
```

**404: Not Found**

If this error occurs, you are trying to change the username of an account that does not own a copy of Minecraft.

```json
{
  "path" : "/minecraft/profile/name/:username",
  "errorMessage" : "Could not find profile",
  "developerMessage" : "Could not find profile"
}
```

**405: Method Not Allowed**

If this error occurs, you have not set the request method to `PUT`.

```json
{
  "path" : "/minecraft/profile/name/:username",
  "errorType" : "METHOD_NOT_ALLOWED",
  "error" : "METHOD_NOT_ALLOWED",
  "errorMessage" : "Method Not Allowed",
  "developerMessage" : "Method Not Allowed"
}
```

**429: Too Many Requests**

If you have sent too many requests in a specific amount of time (3 requests per account and per IP), this error will appear. 

Two responses are noted here, one at the most recent time of testing (June 14, 2021), and the other from some point in January 2021.

```json
// got this error when I last tested this endpoint (June 14, 2021)
{
  "path" : "/minecraft/profile/name/:username"
}

// older error, I didn't get this error the most recent time I tested the endpoint
{
  "path" : "/minecraft/profile/name/:username",
  "errorType" : "TooManyRequestsException",
  "error" : "TooManyRequestsException",
  "errorMessage" : "The client has sent too many requests within a certain amount of time",
  "developerMessage" : "The client has sent too many requests within a certain amount of time"
}
```

**500: Internal Server Error**

This error may be caused due to the API overloading with requests.

```json
{
  "path" : "/minecraft/profile/name/:username",
  "errorType" : "EXTERNAL_API_ERROR",
  "error" : "EXTERNAL_API_ERROR",
  "errorMessage" : "",
  "developerMessage" : ""
}
```
