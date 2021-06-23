# A peek into Bearer tokens
This "article" gives insight into how bearer tokens work, for anyone who doesn't know how bearer tokens are made or what they really are.

There are two kinds of bearer tokens, both of which are made via the [JWT](https://jwt.io/) (JSON Web Token) standard, using the HS256 algorithm.

The two kinds of tokens are MSA (Microsoft Account) tokens and Yggdrasil tokens (Mojang account tokens).

When you log into a Mojang account, you're given a Bearer token to use with any endpoint that requires authentication. The header `Authorization` is where you put the Bearer token - e.g `Authorization: Bearer [bearer_token_here]`.

### Yggdrasil Tokens
Yggdrasil is Mojang's authentication system for Mojang accounts.

An example Yggdrasil authentication token, when decoded (try with [the official JWT site](https://jwt.io/)!), looks like this:

```json
{
  "sub": "2ea6d02ea02ea6e2acf579df2e2eb15f", // internal Mojang account identifier (userId value)
  "yggt": "4f657b3579b950f9bc2efab42c2cc052", // yggdrasil token itself
  "spr": "31e0ccbef5fa4eb988592f30516f65fe", // Minecraft profile UUID (optional)
  "iss": "Yggdrasil-Auth", // issued by "Yggdrasil-Auth"
  "exp": 1607018676, // expiry date
  "iat": 1606845876 // created at
}
```

### MSA Tokens
Microsoft Accounts are what Minecraft players who made their accounts after December 1st, 2020 log in with, so it's good to take a peek inside a MSA token! These are similar yet different in many ways to Yggdrasil tokens.

An example MSA token, when decoded (try with [the official JWT site](https://jwt.io/)!), looks like this:

```json
{
  "xuid": "2533274805829568", // xUID (Xbox user identifier basically)
  "sub": "830491e6-50e9-e9ed-6e8a-7041f4fef585", // determines the token from others
  "nbf": 1606845146, // seems to be same as "iat"
  "roles": [], // ??? perhaps "entitlements" that have been referenced before on API
  "iss": "authentication", // issued by "authentication" (xbox auth) - Yggdrasil normally says "Yggdrasil-Auth"
  "exp": 1606931546, // expire time
  "iat": 1606845146, // created at
  "yuid": "54fc50c1b1bc19dd109fbeff9d11bf41" // something internal with Xbox, not sure
}
```
