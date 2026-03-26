# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Unsafe General-Purpose Hashing

```java
// Unsafe: fast general-purpose hashes are not suitable for password storage.
String hash = DigestUtils.sha256Hex(password);
```

## Password Hashing Primitive

```python
# Safer: use a dedicated password-hashing algorithm with embedded parameters.
password_hash = argon2_hasher.hash(password)
```

## Pepper Separation

```yaml
# Risky: pepper stored alongside app config in the same breach domain.
security:
  password_pepper: hardcoded-pepper-value
```

## Rehash On Login

```javascript
// Safer: upgrade legacy hashes after successful verification.
if (verifyLegacyHash(password, storedHash)) {
  user.passwordHash = hashWithCurrentParameters(password);
}
```