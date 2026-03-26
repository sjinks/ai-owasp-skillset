# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Generic Reset Request Response

```python
# Unsafe: different responses disclose whether the account exists.
if not user:
    return {"message": "email not found"}

# Safer: return the same response regardless of account existence.
return {"message": "if the account exists, a reset link has been sent"}
```

## Reset Token Shape

```typescript
// Safer: random, short-lived, single-use reset tokens.
const token = crypto.randomBytes(32).toString("hex");
await saveResetToken(user.id, hash(token), expiresAt, false);
```

## Token In URL vs Body Use

```javascript
// Risky: reset tokens in query strings leak through logs and referrers.
const resetUrl = `/reset-password?token=${token}`;
```

## Session Cleanup After Reset

```java
// Safer: invalidate active sessions after a successful password reset.
passwordService.resetPassword(user, newPassword);
sessionService.invalidateAllSessions(user.getId());
```