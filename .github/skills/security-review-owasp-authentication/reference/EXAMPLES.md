# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Constant-Time Password Verification

```python
# Unsafe: direct string comparison can leak timing information.
if submitted_hash == stored_hash:
    login_user(user)

# Safer: use a password-verification primitive or constant-time comparison.
if hmac.compare_digest(submitted_hash, stored_hash):
    login_user(user)
```

## Generic Authentication Errors

```java
// Unsafe: response differences disclose account existence.
if (!userExists(email)) {
    return error("account does not exist");
}

// Safer: use a generic failure response for login and recovery paths.
return error("invalid credentials");
```

## Session Rotation After Login

```javascript
// Unsafe: pre-auth session survives into the authenticated state.
req.session.userId = user.id;

// Safer: regenerate the session before binding the authenticated identity.
req.session.regenerate(() => {
  req.session.userId = user.id;
});
```

## Password Reset Token Use

```typescript
// Safer: reset tokens are random, short-lived, and single-use.
const resetToken = crypto.randomBytes(32).toString("hex");
await storeResetToken(user.id, hash(resetToken), expiresAt);
```