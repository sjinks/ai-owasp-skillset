# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Per-IP-Only Throttling Smell

```python
# Risky: IP-only throttling is easy to bypass with distributed traffic.
rate_limit(key=request.remote_addr, limit="10/minute")
```

## Account-Centric Throttling

```python
# Safer: combine account and source signals.
rate_limit(key=f"login:{email}:{request.remote_addr}", limit="5/minute")
rate_limit(key=f"login-account:{email}", limit="20/hour")
```

## Leaked Password Screening

```java
// Safer: reject known-breached passwords during reset or registration.
if (pwnedPasswordService.isCompromised(password)) {
    throw new WeakPasswordException();
}
```

## Suspicious Login Notification

```json
{
  "event": "new_device_login",
  "user_id": "12345",
  "notify_user": true
}
```