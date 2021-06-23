# Get migration token
This endpoint allows an authenticated user to get their migration token, used later for Microsoft Account (MSA) migration.

**NOTE:** These docs are unfinished.

These docs were submitted by Dusks via DM to me on Discord, and I've formatted them and posted here + added a bit more data to them. Thank you, Dusks! Some endpoints have also been documented by fweak.

### Request
- **Method:** `POST`
- **Endpoint:** `/migration/token`
- **Full URL:** `https://api.minecraftservices.com/migration/token`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`
    - `Content-Type: application/json`

The POST body for this request should be similar to this:

```json
{
  "sessionEmail": "cooluser@gmail.com" // current email of the Mojang account
}
```

**Note:** This endpoint can also be used with the `GET` method with no body and no `Content-Type` header, and will return the same data. Status code 400 will not occur with the `GET` method.

### Response
**200: OK**

Got token successfully. Sample response:

```json
eyJhbGciOiJSUzI1NiIsIng1dCI6Ik1waTNjYlliNHJpWWwzcVl1YzFOTm5HeDdvOCIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJodHRwczovL2FwaS5taW5lY3JhZnRzZXJ2aWNlcy5jb20iLCJhdWQiOiJodHRwOi8vbG9naW4ubGl2ZS5jb20iLCJ2ZXIiOiIxLjAiLCJpYXQiOjE2MTg2ODczMzksImV4cCI6MTYxODY4ODUzOSwibmJmIjoxNjE4Njg3MzM5LCJhZGRyZXNzIjp7ImNvdW50cnkiOiJ0d29fbGV0dGVyX2NvdW50cnlfY29kZSJ9LCJiaXJ0aGRhdGUiOiJZWVlZLU1NLUREIiwiZW1haWwiOiJlbWFpbF9vZl9tb2phbmdfYWNjb3VudEBkb21haW4udGxkIiwiZW1haWxfdmVyaWZpZWQiOnRydWV9.qLmgkntcdORU35vplL0989N6kd7-ZKz43ZEYwhMcyrIH13bIs7SlHCbmTDW9s0HN2W9x3RSJZCZ5Eh6K3b5K8PTvfI1QQn4m84UBuZa0GQz0KQcDHhxWd_XTByoqWaEwbSUqWr95WdyMUHU7FqF9-eYnQTtmWw6tF6SxXWYnR7Ub4mQyb96hL1Jo5zF1U6yykipMbnG4i3LAd9SsRpTtgCWPioMa2UWb_ETHDQwvc7Jz1YsPi4oOSqZJpIEqJQO8W1oQt9Th_1XY0W4Qr3iK0LSosVrEr55mB2soNHuQg4FK4pvGoUYfdm_1Yn2ikjpIwB6_PZdNpdK3D9275hUjsA
```

**400: Bad Request**

You have provided an incorrect email as the `sessionEmail`, or neglected to provide a POST body entirely.

```json
// incorrect email (always returned with legacy accounts, even with the correct email or email hash)
{
  "path": "/migration/token",
  "errorMessage": "Supplied e-mail doesn't match account e-mail",
  "developerMessage": "Supplied e-mail doesn't match account e-mail"
}

// did not pass an email 
{
  "path": "/migration/token",
  "errorType": "CONSTRAINT_VIOLATION",
  "error": "CONSTRAINT_VIOLATION",
  "errorMessage": "getMigrationToken.tokenRequest.sessionEmail: must not be blank",
  "developerMessage": "getMigrationToken.tokenRequest.sessionEmail: must not be blank"
}

// blank POST data
{
  "path": "/migration/token",
  "errorType": "Bad Request",
  "error": "Bad Request",
  "errorMessage": "Required Body [tokenRequest] not specified",
  "developerMessage": "Required Body [tokenRequest] not specified"
}
```

**401: Unauthorized**

You provided an invalid Bearer token or neglected to fill in the `Authorization` header entirely.

No body is returned for this response.

**403: Forbidden**

This status code is returned when you try to get your token with a MSA (already migrated or newly created).

```json
{
  "path": "/migration/token",
  "errorType": "FORBIDDEN",
  "error": "FORBIDDEN"
}
```

**500: Internal Server Error**

In testing as of June 20, 2021, I have not observed this to occur with unmigrated / legacy accounts as previously noted here.

```json
{
  "path": "/migration/token"
}
```
