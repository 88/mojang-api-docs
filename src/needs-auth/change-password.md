# Change user password
This endpoint allows an authenticated user on a Mojang account to change their password.

### Request
- **Method:** `PUT`
- **Endpoint:** `/users/password`
- **Full URL:** `https://api.mojang.com/users/password`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`
    - `Content-Type: application/json`

There are no URL parameters. Sample PUT body below:

```json
{
  "oldPassword" : "password12345", // current password
  "password" : "6ipo8Y%GMxM544V%$h*&M*#%7!D*897%" // new password
}
```

### Response
**204: No Content**

The password has been changed successfully. You must log in to your account again. There is no response for this status code.

**400: Bad Request**

This status code is returned if the old password provided is invalid, or if you have supplied an invalid JSON body. It can also be returned if you are trying to change your account's password without answering the security questions.

```json
// when old password is invalid
{
  "error" : "IllegalArgumentException",
  "errorMessage" : "Old password invalid."
}

// supplied invalid JSON body
{
  "error" : "JsonParseException",
  "errorMessage" : "Unexpected character ('\"' (code 34)): was expecting comma to separate Object entries\n at [Source: (org.eclipse.jetty.server.HttpInputOverHTTP); line: 1, column: 29]"
}

// this body (apparently) is sent in some cases. not sure what triggers it.
{
  "error" : "IllegalArgumentException",
  "errorMessage" : "Unsecure request"
}
```

**401: Unauthorized**

You have not provided a valid JWT / auth token, or you failed to answer security questions, or you have neglected to provide the `Authorization` header at all.

```json
{
  "error" : "Unauthorized",
  "errorMessage" : "The request requires user authentication"
}
```
