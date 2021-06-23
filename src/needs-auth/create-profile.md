# Create a Minecraft profile
This endpoint allows a user with a redeemed giftcode on their account to create a Minecraft profile.

This endpoint has a ratelimit of 6 requests per account and 60 requests per IP.

### Request
- **Method:** `POST`
- **Endpoint:** `/minecraft/profile`
- **Full URL:** `https://api.minecraftservices.com/minecraft/profile`
- **Headers:**
    - `Accept: application/json`
    - `Authorization: Bearer [JWT/auth token here]`

As the POST body, simply supply your desired username.

```json
{
  "profileName" : "Desired_IGN"
}
```

### Response
**200: OK**

Successfully created a new profile! Here is a sample response:

```json
{
  "id" : "cdb5aee80f904fdda63ba16d38cd6b3b", // UUID of account
  "name" : "lukethehacker23", // username
  "skins" : [ {
    "id" : "b647c354-16b0-47e3-8340-5d328b4215c1", // skin ID - always will be the same for this skin
    "state" : "ACTIVE", // if skin is enabled or not
    "url" : "http://textures.minecraft.net/texture/3b60a1f6d562f52aaebbf1434f1de147933a3affe0e764fa49ea057536623cd3", // skin texture URL
    "variant" : "SLIM", // model of skin (steve/alex aka CLASSIC/SLIM)
    "alias" : "ALEX" // assuming this is only for STEVE/ALEX skin.
  } ],
  "capes" : [ {
    "id" : "402d2b1a-48a5-4177-8173-85b0d1d04889", // cape ID - always will be the same for this cape
    "state" : "ACTIVE", // if cape is enabled or not
    "url" : "http://textures.minecraft.net/texture/153b1a0dfcbae953cdeb6f2c2bf6bf79943239b1372780da44bcbb29273131da", // cape texture URL
    "alias" : "Minecon2013" // cape name
  } ]
}
```

**400: Bad Request**

Something went wrong while creating the profile. Perhaps you already own a copy of Minecraft: Java Edition, or you supplied an invalid username, or something else. Here are the errors we've stumbled into:

```json
// if you already own a copy of Minecraft: Java Edition
{
    "path" : "/minecraft/profile",
    "errorType" : "BAD_REQUEST",
    "error" : "BAD_REQUEST",
    "details" : {
        "status" : "ALREADY_REGISTERED"
    }
}

// if you don't own a copy of Minecraft: Java Edition
{
  "path" : "/minecraft/profile",
  "errorType" : "BAD_REQUEST",
  "error" : "BAD_REQUEST",
  "details" : {
    "status" : "NOT_ENTITLED"
  }
}

// if you supply a username that already exists
{
  "path" : "/minecraft/profile",
  "errorType" : "BAD_REQUEST",
  "error" : "BAD_REQUEST",
  "details" : {
    "status" : "DUPLICATE"
  }
}

// if you supply a username that is blocked by Mojang's username filter
{
  "path" : "/minecraft/profile",
  "errorType" : "BAD_REQUEST",
  "error" : "BAD_REQUEST",
  "details" : {
    "status" : "NOT_ALLOWED"
  }
}

// if you supplied a username of invalid length
{
  "path" : "/minecraft/profile",
  "errorType" : "CONSTRAINT_VIOLATION",
  "error" : "CONSTRAINT_VIOLATION",
  "errorMessage" : "createProfile.publicCreateProfileDTO.profileName: size must be between 3 and 16, createProfile.publicCreateProfileDTO.profileName: Invalid profile name",
  "developerMessage" : "createProfile.publicCreateProfileDTO.profileName: size must be between 3 and 16, createProfile.publicCreateProfileDTO.profileName: Invalid profile name"
}

// if you supplied a username with invalid characters
{
    "path": "/minecraft/profile",
    "errorType": "CONSTRAINT_VIOLATION",
    "error": "CONSTRAINT_VIOLATION",
    "errorMessage": "createProfile.publicCreateProfileDTO.profileName: Invalid profile name",
    "developerMessage": "createProfile.publicCreateProfileDTO.profileName: Invalid profile name"
}
```

**401: Unauthorized**

You have not provided a valid JWT / auth token, or you have neglected to provide the `Authorization` header at all.

```json
{
  "path" : "/minecraft/profile",
  "errorType" : "UnauthorizedOperationException",
  "error" : "UnauthorizedOperationException",
  "errorMessage" : "Unauthorized",
  "developerMessage" : "Unauthorized"
}
```

**429: Too Many Requests**

You have exceeded the endpoint's ratelimit. Wait 60 seconds, then try again.

```json
{
  "path" : "/minecraft/profile",
  "errorType" : "TooManyRequestsException",
  "error" : "TooManyRequestsException",
  "errorMessage" : "The client has sent too many requests within a certain amount of time",
  "developerMessage" : "The client has sent too many requests within a certain amount of time"
}
```

**500: Internal Server Error**

If this status code is returned, the API is most likely being overloaded.

```json
{
  "path" : "/minecraft/profile",
  "errorType" : "Connect Error: Acquire operation took longer then configured maximum time",
  "error" : "Connect Error: Acquire operation took longer then configured maximum time"
}

{
  "path" : "/minecraft/profile",
  "errorType" : "Connect Error: Too many outstanding acquire operations",
  "error" : "Connect Error: Too many outstanding acquire operations"
}
```
