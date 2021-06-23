# Username availability (partially accurate)
This is an undocumented endpoint that allows for the checking of usernames that have potentially been blocked, unlike `/users/profiles/minecraft/:username`.

Keep in mind, this endpoint is not located on the normal Mojang API domain - instead, it is located on `account.mojang.com`.

### A quick word on blocking and MSAs
Blocking can happen either one of two ways - a username sniper has blocked the name for 24 hours on an empty Mojang account, or the username has been blocked by Mojang's inappropriate name filter for whatever reason. The reasons names are blocked aren't public, but this is the best way to check if a name is blocked.

If the name returns `204` on the normal Username to UUID endpoint, yet returns `TAKEN` here, it's either blocked or is pseudo-hard-deleted - the UUID exists in some parts of the API but not others. The username availability endpoint that does need authentication is more accurate than this endpoint, since it shows pseudo-hard-deleted accounts under `TAKEN` and blocked usernames under `NOT_ALLOWED`.

Something else of note - this endpoint no longer properly functions with the introduction of Microsoft accounts. They falsely appear as `AVAILABLE` on this endpoint. Microsoft accounts that were migrated from Mojang accounts function correctly, only newly created Microsoft accounts do not function with this endpoint.

### Request
- **Method:** `GET`
- **Endpoint:** `/available/minecraft/:username`
- **Full URL:** `https://account.mojang.com/available/minecraft/:username`

Checks if a username is available or taken. Will return TAKEN for every 1 and 2-character username, any username with invalid characters, or any name with more than 16 characters.

The only URL parameter here is `username`, the username that we want to check availability for.

### Response
**200: OK**

Unless there is a server error this is the only response code for this endpoint. There are two possible responses - AVAILABLE or TAKEN, that's it. It's a very small endpoint but does its job well.

```json
// if the name is legitimately available
AVAILABLE

// if the name is taken or blocked.
TAKEN
```
