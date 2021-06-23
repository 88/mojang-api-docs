# Check migration status
This endpoint allows an authenticated user to check if they are eligible for MSA migration.

These docs were submitted by Dusks via DM to me on Discord, and I've formatted them and posted here + added a bit more data to them. Thank you, Dusks! Some endpoints have also been documented by fweak.

### Request
- **Method:** `GET`
- **Endpoint:** `/rollout/v1/msamigration`
- **Full URL:** `https://api.minecraftservices.com/rollout/v1/msamigration`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`

### Response
**200: OK**

Successfully fetched migration status. Sample response:

```json
{
  "feature": "msamigration",
  "rollout": false
}
```

**401: Unauthorized**

You provided an invalid Bearer token or neglected to fill in the `Authorization` header entirely.

No body is returned for this response.
