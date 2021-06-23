# View your profile information
This endpoint allows for an authenticated user to view information about their Minecraft profile.

### Request
- **Method:** `GET`
- **Endpoint:** `/minecraft/profile`
- **Full URL:** `https://api.minecraftservices.com/minecraft/profile`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`

### Response
**200: OK**

No errors were encountered when fetching your profile information. Sample response:

```json
{
  "id" : "cdb5aee80f904fdda63ba16d38cd6b3b", // UUID of account
  "name" : "lukethehacker23", // username
  "skins" : [ {
    "id" : "b647c354-16b0-47e3-8340-5d328b4215c1", // skin ID - always will be the same for this skin
    "state" : "ACTIVE", // if skin is enabled or not
    "url" : "http://textures.minecraft.net/texture/3b60a1f6d562f52aaebbf1434f1de147933a3affe0e764fa49ea057536623cd3", // skin texture URL
    "variant" : "SLIM", // model of skin (steve/alex aka CLASSIC/SLIM)
    "alias" : "ALEX" // assuming this is only for STEVE/ALEX skin. doesn't show up for some reason for some accounts.
  } ],
  "capes" : [ {
    "id" : "402d2b1a-48a5-4177-8173-85b0d1d04889", // cape ID - always will be the same for this cape
    "state" : "ACTIVE", // if cape is enabled or not
    "url" : "http://textures.minecraft.net/texture/153b1a0dfcbae953cdeb6f2c2bf6bf79943239b1372780da44bcbb29273131da", // cape texture URL
    "alias" : "Minecon2013" // cape name
  } ]
}
```

**401: Unauthorized**

You provided an invalid Bearer token or neglected to fill in the `Authorization` header entirely.

```json
{
  "path" : "/minecraft/profile",
  "errorType" : "UnauthorizedOperationException",
  "error" : "UnauthorizedOperationException",
  "errorMessage" : "Unauthorized",
  "developerMessage" : "Unauthorized"
}
```

**404: Not Found**

You are trying to view the profile of an account that does not own a copy of Minecraft.

```json
{
  "path" : "/minecraft/profile",
  "errorType" : "NOT_FOUND",
  "error" : "NOT_FOUND",
  "errorMessage" : "The server has not found anything matching the request URI",
  "developerMessage" : "The server has not found anything matching the request URI"
}
```
