# Check if you own a copy of Minecraft
This endpoint allows an authenticated user to check if they own a copy of Minecraft.

### Request
- **Method:** `GET`
- **Endpoint:** `/entitlements/mcstore`
- **Full URL:** `https://api.minecraftservices.com/entitlements/mcstore`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`

### Response
**200: OK**

Successfully retrieved the account entitlements.

```json
// when you do own a copy of the game
{
  "items": [
    {
      "name": "product_minecraft",
      "signature": "JWT here"
    },
    {
      "name": "game_minecraft",
      "signature": "JWT here"
    }
  ],
  "signature": "JWT here",
  "keyId": "1"
}

// when you do not own a copy of the game
{
  "items": [],
  "signature": "JWT here",
  "keyId": "1"
}
```

If you do not own a copy of Minecraft, the `items` array will be empty.

Inside the `signature` JWTs for `product_minecraft` and `game_minecraft`, the body is:

```json
{
  "signerId": "2575459622728545",
  "name": "game_minecraft" // can also be product_minecraft
}
```

Inside the `signature` JWT that is outside the `items` array, the body is:

```json
{
  "entitlements": [
    {
      "name": "product_minecraft"
    },
    {
      "name": "game_minecraft"
    }
  ],
  "signerId": "2575459622728545",
  "nbf": 1624461990,
  "exp": 1624634970,
  "iat": 1624462170
}
```

**400: Bad Request**

This status code is returned when you are trying to use a Mojang account with this endpoint.

```json
{
  "path": "/entitlements/mcstore",
  "errorType": "Bad Request",
  "error": "Bad Request",
  "errorMessage": "Required JWT [user] not specified",
  "developerMessage": "Required JWT [user] not specified"
}
```
