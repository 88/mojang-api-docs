# Username availability (accurate)
This endpoint allows for the checking of usernames that have potentially been blocked, unlike `/users/profiles/minecraft/:username`.

### A quick word on blocking
Blocking can happen either one of two ways - a username sniper has blocked the name for 24 hours on an empty Mojang account, or the username has been blocked by Mojang's inappropriate name filter for whatever reason. The reasons names are blocked aren't public, but this is the best way to check if a name is blocked.

### Request
- **Method:** `GET`
- **Endpoint:** `/minecraft/profile/name/:username/available`
- **Full URL:** `https://api.minecraftservices.com/minecraft/profile/name/:username/available`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`

Checks if a username is available or taken. Will return TAKEN for every 1 and 2-character username, any username with invalid characters, or any name with more than 16 characters.

The only URL parameter here is `username`, the username that we want to check availability for.

### Response
**200: OK**

This is the only status code returned, unless there's another error e.g you're ratelimited or something of that sort.

```json
// if the name is available to claim
{
  "status" : "AVAILABLE"
}

// if the name is taken - this could show for an account that appears "available" on NameMC or other websites.
// this just means that the profile with the username in question is pseudo-hard-deleted.
{
  "status" : "DUPLICATE"
}

// if the name is blocked by Mojang's username filter
{
  "status" : "NOT_ALLOWED"
}
```

**401: Unauthorized**

You have not provided a valid JWT / auth token, or you have neglected to provide the `Authorization` header at all.

```json
{
  "path" : "/minecraft/profile/name/:username/available",
  "errorType" : "UnauthorizedOperationException",
  "error" : "UnauthorizedOperationException",
  "errorMessage" : "Unauthorized",
  "developerMessage" : "Unauthorized"
}
```

**405: Method Not Allowed**

This error occurs when you have not set the HTTP method of the request to `GET`.

```json
{
  "path": "/minecraft/profile/name/:username/available",
  "errorType": "METHOD_NOT_ALLOWED",
  "error": "METHOD_NOT_ALLOWED",
  "errorMessage": "Method Not Allowed",
  "developerMessage": "Method Not Allowed"
}
```

**429: Too Many Requests**

If you have sent too many requests in a specific amount of time, this error will appear.

```json
{
  "path" : "/minecraft/profile/name/:username/available",
  "errorType" : "TooManyRequestsException",
  "error" : "TooManyRequestsException",
  "errorMessage" : "The client has sent too many requests within a certain amount of time",
  "developerMessage" : "The client has sent too many requests within a certain amount of time"
}
```
