# Username to UUID (POST)
This endpoint allows users to send up to 10 usernames in an array and return all valid UUIDs.

### Request
- **Method:** `POST`
- **Endpoint:** `/profiles/minecraft`
- **Full URL:** `https://api.mojang.com/profiles/minecraft`
- **Headers:**
    - `Content-Type: application/json`

The JSON body of the request must look similar to this (of course, supplying your own usernames):

```json
[
  "thx",
  "Dusks",
  "tanpug",
  "D__G",
  "Gr8_Escape",
  "Paradox",
  "Ooh",
  "Tremendous",
  "Hexene",
  "emotional"
]
```

### Response
**200: OK**

There were no errors with the request. You may still get a `200 OK` response if none of the names you provided correspond to a valid Minecraft profile - you will just get an empty array. Sample responses are below:

```json
// no valid Minecraft profiles were found for any of the requested usernames
[]

// valid Minecraft profiles were found - they are sorted when returned
[
  {
    "id": "6d752bb0ef41432a825a4d44185de121",
    "name": "Gr8_Escape"
  },
  {
    "id": "7211ae0bdb3f4680ae7cb34f2c4befc0",
    "name": "Hexene"
  },
  {
    "id": "8af296533b6844d085932742dca689c9",
    "name": "Dusks"
  },
  {
    "id": "c7b3d49c580c4af2a824ca07b37ff2f9",
    "name": "D__G"
  },
  {
    "id": "b3d0b85c9daf43d387f72696bdb618a1",
    "name": "Paradox"
  },
  {
    "id": "f0d9de88bbb54c9eae429cd8fbd693ab",
    "name": "tanpug"
  },
  {
    "id": "42d414a31d3e456bb08864e254abfd54",
    "name": "emotional"
  },
  {
    "id": "3e290b0243cf47f9801fa162ae7f0ff6",
    "name": "Ooh"
  },
  {
    "id": "44717d8d18c8430184defff3a92167a0",
    "name": "thx"
  },
  {
    "id": "7d96137e15a24f09b79f2366199d822e",
    "name": "Tremendous"
  }
]
```

**Note:** You may have noticed that Microsoft profiles are returned first in the array, and sorted separately from Mojang profiles. Legacy profiles are returned last in the array and are also sorted separately. 

The order goes: MSA (sorted alphabetically, case-insensitive) --> Mojang (sorted alphabetically, case-insensitive) --> Legacy (sorted alphabetically, case-insensitive)

**400: Bad Request**

You could have gotten this error either because you've supplied an invalid username as one of the names in the array you sent as the POST body, because you supplied invalid JSON as the POST body, or because you supplied more than 10 names in the POST body.

A list of invalid characters known to trigger this status code: `#&\|/"`

Names that are 26 characters and longer will also trigger this status code. 

Any name that is under 25 characters and fits the regex `^(?=.*?(\w|^)(\w|$))((?![#&\\\|\/"])\w){1,25}$` will not trigger this error.

```json
// invalid length
{
  "error" : "BadRequestException",
  "errorMessage" : "obviouslyanamethatistoolongforminecraft is invalid"
}

// invalid character
{
  "error" : "BadRequestException",
  "errorMessage" : "& is invalid"
}

// invalid JSON
{
  "error" : "JsonMappingException",
  "errorMessage" : "Unexpected character ('\"' (code 34)): was expecting comma to separate Array entries\n at [Source: (org.eclipse.jetty.server.HttpInputOverHTTP); line: 1, column: 118] (through reference chain: java.lang.Object[][7])"
}

// more than 10 names
{
  "error" : "IllegalArgumentException",
  "errorMessage" : "Not more that 10 profile name per call is allowed."
}
```

**405: Method Not Allowed**

You did not make the request a POST request.

```json
{
  "error" : "Method Not Allowed",
  "errorMessage" : "The method specified in the request is not allowed for the resource identified by the request URI"
}
```

**415: Unsupported Media Type**

When encountering this error, you have most likely neglected to add the `Content-Type: application/json` header to your request.

```json
{
  "error" : "Unsupported Media Type",
  "errorMessage" : "The server is refusing to service the request because the entity of the request is in a format not supported by the requested resource for the requested method"
}
```

**429: Too Many Requests**

If you get this error, you have sent too many requests and must wait at least 30 seconds before sending another.

```json
{
  "error" : "TooManyRequestsException",
  "errorMessage" : "The client has sent too many requests within a certain amount of time"
}
```
