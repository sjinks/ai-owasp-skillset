# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Hardcoded Secret

```javascript
// Unsafe: secret embedded directly in source code.
const apiKey = "prod-live-key-123";
```

## Scoped Secret Retrieval

```python
# Safer: request only the secret needed for the current workload and environment.
db_password = secrets_client.get_secret("payments/prod/db-password")
```

## Pipeline Log Exposure

```yaml
# Risky: secret echoed into CI logs.
- run: echo "$PRODUCTION_TOKEN"
```

## Rotation-Ready Consumption

```go
// Safer: reload secrets from the manager instead of caching them for process lifetime.
func currentSigningKey() string {
    return secretManager.Get("signing-key")
}
```