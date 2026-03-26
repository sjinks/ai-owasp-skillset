# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## PKCE for Public Clients

```javascript
// Unsafe: public client starts authorization without PKCE.
const authorizationUrl = `/authorize?client_id=${clientId}&response_type=code`;

// Safer: send a code challenge and verify the matching code verifier later.
const authorizationUrl = `/authorize?client_id=${clientId}&response_type=code&code_challenge=${challenge}&code_challenge_method=S256`;
```

## Redirect URI Validation

```java
// Unsafe: prefix or substring matching allows redirect abuse.
if (redirectUri.startsWith("https://app.example.com")) {
    allow();
}

// Safer: compare against exact registered redirect URIs.
if (registeredRedirectUris.contains(redirectUri)) {
    allow();
}
```

## State Validation

```python
# Unsafe: token exchange continues without checking the original state value.
exchange_code(request.args["code"])

# Safer: reject the callback if the bound state value does not match.
if request.args["state"] != session["oauth_state"]:
    raise SecurityError("invalid state")
exchange_code(request.args["code"])
```

## Forbidden Grant Smell

```yaml
# Risky: resource owner password credentials should not be enabled in modern deployments.
oauth:
  allowed_grants:
    - password
```