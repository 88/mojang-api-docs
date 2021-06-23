# Endpoint title
This endpoint allows [an authenticated user/a user] to [function].

### Request
- **Method:** `METHOD`
- **Endpoint:** `/path/to/endpoint/:parameter`
- **Full URL:** `https://sub.domain.tld/path/to/endpoint/:parameter`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]` (if needed)
    - `Content-Type: application/json` (if needed)

**IF REQUEST IS POST/PUT AND NEEDS A BODY:**

The PUT body for this request should be similar to this:

```json
{
  "key": "value" // comment explaining what this is & what it does
}
```

### Response
**success_status_code: Description**

[did action] successfully. Sample response:

```json
{
  "key": "value", // comment explaining what this is & what it does
  "key2": "value2" // comment explaining what this is & what it does
}
```

**error_status_code: Description**

Explanation of error and why it occurs.

```json
// error JSON response here
```

**401: Unauthorized** (if needed)

You provided an invalid Bearer token or neglected to fill in the `Authorization` header entirely.

```json
// unauthorized JSON response here
```
