# View your profile information (old) - GET
This endpoint allows for an authenticated user to view information about their Minecraft profile.

There are two endpoints that do this task. Both are identical in function, however the URLs slightly differ.

### Request
- **Method:** `GET`
- **Endpoint 1:** `/user/profiles`
- **Endpoint 2:** `/user/profiles/agent/minecraft`
- **Full URL 1:** `https://api.mojang.com/user/profiles`
- **Full URL 2:** `https://api.mojang.com/user/profiles/agent/minecraft`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`

You must have answered your security questions described in the Security Question-Answer Flow article in order to use this endpoint.

### Response
**200: OK**

No errors were encountered when fetching your profile information. Sample response:

```json
[
  {
    "agent": "minecraft", // "scrolls" for Scrolls
    "id": "f933f85fc46046038e5f579ee1c919c5", // UUID of profile
    "name": "iiiiisssssiiiiii", // profile username
    "userId": "ff0344e1524fba45b6ce61fdd2da153c", // internal Mojang account identifier (not UUID)
    "createdAt": 1587576665000, // date the profile was made premium
    "legacyProfile": false, // is the profile legacy / unmigrated?
    "suspended": false, // ???
    "tokenId": "21580755", // ??? internal identifier, only appears for accounts created as Mojang accounts, NOT accounts that were previously unmigrated/legacy
    "paid": true, // is the profile paid?
    "migrated": false // ??? this appears as false for migrated (Mojang) accounts as well
  }
]
```

**401: Unauthorized**

This error occurs when you either haven't answered your security questions, or when you've passed an invalid or expired Bearer token in the `Authorization` header.

```json
{
  "error": "Unauthorized",
  "errorMessage": "The request requires user authentication"
}
```
