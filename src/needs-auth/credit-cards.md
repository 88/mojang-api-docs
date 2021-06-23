# View credit card information
This endpoint allows an authenticated user to see any credit card information that they have stored.

You must have answered your security questions described in the Security Question-Answer Flow article in order to use this endpoint.

### Request
- **Method:** `GET`
- **Endpoint:** `/creditcards`
- **Full URL:** `https://api.mojang.com/creditcards`
- **Headers:**
    - `Authorization: Bearer [JWT/auth token here]`

### Response
**200: OK**

No errors were encountered while fetching credit card information. If you have not saved any credit cards, the returned JSON will be an empty array, `[]`. Sample responses below:

```json
[
  {
    "id" : "a85887bf0ffd80d5a6addd6ff5aa278a", // credit card ID
    "storedInVault" : true, // is it stored?
    "billingAddress" : { // address info
      "id" : "30459174", // Not sure
      "userId" : "88a4eeb5bd5a4640aa3865878aec6d6b", // internal Mojang account identifier (NOT UUID)
      "countryCode" : "US", // two-letter country code
      "postalCode" : "95014", // postal code
      "creditCardId" : "145447b03f0d43c5a6accd60f5aa2741", // same as the "id" key we saw above
      "newAddress" : false // is it a new address?
    },
    "userId" : "88a4eeb5bd5a4640aa3865878aec6d6b", // internal Mojang account identifier
    "last4" : "4272", // last 4 digits of the card
    "cardType" : "Visa", // visa/mastercard/etc
    "provider" : "braintree", // payment provider used - this could also be "moneybookers", "ayden", "skrill", "dibs", "paypal"
    "paymentInfo" : "434283**4272", // redacted credit card number
    "createdAt" : 1597463065000, // when was the credit card added?
    "storedInVaultAt" : 1597463067000, // when was the credit card stored?
    "newCreditCard" : false // is this a new credit card?
  }
]
```

**401: Unauthorized**

This error occurs when you either haven't answered your security questions, or when you've passed an invalid or expired Bearer token in the `Authorization` header.

```json
{
  "error" : "UnauthorizedOperationException",
  "errorMessage" : "User not authenticated"
}
```
