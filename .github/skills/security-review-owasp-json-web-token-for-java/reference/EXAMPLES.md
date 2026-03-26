# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Algorithm Pinning

```java
// Unsafe: verifier behavior depends on token-controlled header values.
DecodedJWT jwt = JWT.decode(token);
Algorithm algorithm = Algorithm.HMAC256(jwt.getAlgorithm());

// Safer: pin the expected algorithm and key material in verifier configuration.
Algorithm algorithm = Algorithm.HMAC256(secretKeyBytes);
JWTVerifier verifier = JWT.require(algorithm).withIssuer("auth.example").build();
```

## Weak Secret Handling

```java
// Unsafe: short string literals make weak HMAC secrets.
Algorithm algorithm = Algorithm.HMAC256("secret");
```

## Claim Validation

```java
// Safer: reject tokens that do not match the expected issuer and audience.
JWTVerifier verifier = JWT.require(algorithm)
    .withIssuer("auth.example")
    .withAudience("payments-api")
    .build();
```

## Browser Storage Tradeoff

```javascript
// Risky: long-lived bearer tokens in localStorage are exposed to XSS.
localStorage.setItem("access_token", token);

// Safer: if cookies are used, pair them with HttpOnly, Secure, and CSRF defenses.
document.cookie = "access_token=...; Secure; HttpOnly; SameSite=Strict";
```