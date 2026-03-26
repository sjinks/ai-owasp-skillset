# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Signature Verification

```python
# Safer: verify the signed payload using a server-side secret before processing.
expected = hmac.new(secret, request.data, hashlib.sha256).hexdigest()
if not hmac.compare_digest(expected, request.headers["X-Signature"]):
    raise SecurityError("invalid signature")
```

## Replay Defense

```javascript
// Risky: webhook delivery IDs are not tracked, so the same event can be replayed.
processWebhook(req.body);

// Safer: reject duplicate delivery IDs or expired timestamps.
if (seenDeliveryIds.has(req.headers["x-delivery-id"])) throw new Error("replay");
```

## IP Allowlist Limitation

```nginx
# Risky: IP allowlists alone are brittle and should not replace message authentication.
allow 203.0.113.0/24;
deny all;
```

## Fast Acknowledgement

```go
// Safer: acknowledge verified events quickly and process them asynchronously.
go queueVerifiedEvent(event)
w.WriteHeader(http.StatusAccepted)
```