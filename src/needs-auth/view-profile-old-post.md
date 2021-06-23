# View your profile information (old) - POST
This endpoint allows for an authenticated user to view information about their Minecraft profile.

There are two endpoints that do this task. Both are identical in function, however the URLs slightly differ.

### Request
- **Method:** `POST`
- **Endpoint 1:** `/user/profiles`
- **Endpoint 2:** `/user/profiles/agent/minecraft`
- **Full URL 1:** `https://api.mojang.com/user/profiles`
- **Full URL 2:** `https://api.mojang.com/user/profiles/agent/minecraft`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`
    - `Content-Type: application/json`

You must have answered your security questions described in the Security Question-Answer Flow article in order to use this endpoint.

The POST body must be:

```json
{
  "name" : "IGN tied to your Bearer token"
}
```

You can also input literally anything else in the JSON body, e.g `{"test" : "test"}`, and it'll show your profile information, unless you provide a JSON body similar to `{"name" : "name that is not tied to your Bearer token"}`. In that case it will output an empty array.

### Response
**200: OK**

No errors were encountered when fetching your profile information. If you provided an IGN that is not tied to your Bearer token, the array will be empty. Sample response:

```json
// for an IGN that is tied to your Bearer token
[
  {
    "agent" : "minecraft", // "scrolls" for Scrolls
    "id" : "f933f85fc46046038e5f579ee1c919c5", // UUID of profile
    "name" : "iiiiisssssiiiiii", // profile username
    "userId" : "ff0344e1524fba45b6ce61fdd2da153c", // internal Mojang account identifier (not UUID)
    "createdAt" : 1587576665000, // date the profile was made premium
    "legacyProfile" : false, // is the profile legacy / unmigrated?
    "suspended" : false, // ???
    "tokenId" : "10026860", // not sure, only shows up if your account has always been migrated, not if your account was previously unmigrated / legacy, or currently legacy.
    "paid" : true, // is the profile paid?
    "migrated" : false // ??? this appears as false for migrated (Mojang) accounts as well
  }
]

// NOTE: it's thought that if you have multiple Minecraft profiles tied to one Mojang account (like Marc does), the array will have multiple elements each with their own Minecraft profile.

// for an IGN that is not tied to your Bearer token
[]
```

**401: Unauthorized**

This error occurs when you either haven't answered your security questions, or when you've passed an invalid or expired Bearer token in the `Authorization` header.

```json
{
  "error": "Unauthorized",
  "errorMessage": "The request requires user authentication"
}
```
