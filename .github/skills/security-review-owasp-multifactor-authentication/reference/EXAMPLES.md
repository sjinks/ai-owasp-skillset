# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## OTP Single-Use Enforcement

```python
# Unsafe: the same OTP can be reused until it expires.
if submitted_code == stored_code and now < expires_at:
    return success()

# Safer: invalidate the OTP immediately after successful verification.
if submitted_code == stored_code and now < expires_at:
    mark_code_as_used(challenge_id)
    return success()
```

## MFA Coverage Gap

```javascript
// Risky: web login enforces MFA but API token login skips it entirely.
if (channel === "web") {
  requireMfaChallenge(user);
}
```

## Factor Replacement Protection

```java
// Safer: require a trusted existing factor before replacing the enrolled factor.
requireRecentPasswordCheck(user);
requireExistingFactorChallenge(user);
replaceFactor(user, newFactor);
```

## Recovery Channel Weakness

```yaml
# Risky: email-only recovery can become a full MFA bypass for high-risk accounts.
mfa:
  recovery_methods:
    - email_link
```