# Link an account
This endpoint allows an authenticated user to link their MSA to their Mojang account.

**NOTE:** These docs are unfinished.

These docs were submitted by Dusks via DM to me on Discord, and I've formatted them and posted here + added a bit more data to them. Thank you, Dusks! Some endpoints have also been documented by fweak.

### Request
- **Method:** `POST`
- **Endpoint:** `/migration/link`
- **Full URL:** `https://api.minecraftservices.com/migration/link`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`

The POST body should be similar to this:
```json
{
  "identityToken": "XBL3.0 x=XUID_HERE;JWT_HERE"
}
```

### Response
**400: Bad Request**

You are given this response if you neglect to insert a POST body or if the `identityToken` is blank.

```json
// no POST body
{
  "path": "/migration/link",
  "errorType": "BAD_REQUEST",
  "error": "BAD_REQUEST",
  "errorMessage": "Bad Request",
  "developerMessage": "Bad Request"
}

// identityToken is blank
{
  "path": "/migration/link",
  "errorType": "CONSTRAINT_VIOLATION",
  "error": "CONSTRAINT_VIOLATION",
  "errorMessage": "linkAsync.migrationRequest.identityToken: must not be blank",
  "developerMessage": "linkAsync.migrationRequest.identityToken: must not be blank"
}
```

**401: Unauthorized**

You provided an invalid Bearer token or neglected to fill in the `Authorization` header entirely. Sample response:

```json
{
  "path": "/migration/link",
  "errorType": "UNAUTHORIZED",
  "error": "UNAUTHORIZED"
}
```

**405: Method Not Allowed**

You used the wrong HTTP method for this request. Sample response:

```json
{
  "path": "/migration/link",
  "errorType": "METHOD_NOT_ALLOWED",
  "error": "METHOD_NOT_ALLOWED",
  "errorMessage": "Method Not Allowed",
  "developerMessage": "Method Not Allowed"
}
```

**409: Conflict**

This is the current response for this endpoint when given a valid `identityToken`. Sample response:

```json
{
  "path": "/migration/link",
  "errorType": "NOT_ELIGIBLE",
  "error": "NOT_ELIGIBLE"
}
```