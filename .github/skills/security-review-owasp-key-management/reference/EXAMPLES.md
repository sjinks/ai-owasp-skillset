# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Plaintext Key Material in Config

```yaml
# Unsafe: raw signing key stored directly in application configuration.
crypto:
  signing_key: |
    -----BEGIN PRIVATE KEY-----
    ...
```

## Purpose Separation

```java
// Risky: one key reused for unrelated purposes.
Key appKey = loadKey("primary-key");
signToken(appKey, claims);
encryptData(appKey, plaintext);
```

## Wrapped Export Pattern

```python
# Safer: export only wrapped key material under a stronger transport or wrapping key.
wrapped_key = hsm.wrap_key(key_id="db-encryption-key", wrapping_key_id="backup-wrap-key")
```

## Trust Store Injection Smell

```bash
# Risky: dynamically appending unverified roots to a trust store.
cat user_supplied_ca.pem >> /etc/ssl/certs/custom-ca-bundle.pem
```