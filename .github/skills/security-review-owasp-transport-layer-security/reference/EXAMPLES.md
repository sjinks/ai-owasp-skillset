# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Disabled Certificate Validation

```java
// Unsafe: TLS peer validation disabled entirely.
TrustManager[] trustAll = new TrustManager[] { new InsecureTrustManager() };
```

## Strong HTTPS Redirect and HSTS

```nginx
# Safer: force HTTPS and advertise strict transport requirements.
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
return 301 https://$host$request_uri;
```

## Weak TLS Policy Smell

```yaml
# Risky: legacy protocol versions remain enabled.
tls:
  min_version: TLS1.0
```

## mTLS Peer Binding

```go
// Safer: require and verify client certificates for service-to-service trust.
tlsConfig := &tls.Config{
    ClientAuth: tls.RequireAndVerifyClientCert,
}
```