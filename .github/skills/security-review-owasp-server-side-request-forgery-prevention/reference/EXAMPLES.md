# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Direct User-Controlled Fetch

```python
# Unsafe: user input becomes the full outbound target.
response = requests.get(request.args["url"])

# Safer: accept only a constrained identifier and map it to an approved target.
allowed_hosts = {"billing": "https://billing.internal.example"}
target = allowed_hosts.get(request.args["service"])
response = requests.get(target, allow_redirects=False)
```

## Parsed Host Validation

```javascript
// Unsafe: substring checks do not prove the effective destination is trusted.
if (url.includes("example.com")) {
  await fetch(url);
}

// Safer: validate parsed output, then compare the canonical host.
const parsedUrl = new URL(url);
if (parsedUrl.hostname === "api.example.com") {
  await fetch(parsedUrl, { redirect: "error" });
}
```

## Redirect Revalidation

```go
// Unsafe: automatic redirects can jump to internal targets after initial validation.
client := &http.Client{}

// Safer: fail closed or revalidate every redirected location.
client := &http.Client{
    CheckRedirect: func(req *http.Request, via []*http.Request) error {
        return http.ErrUseLastResponse
    },
}
```

## Metadata Blocking

```java
// Safer: reject private and link-local targets before the request is sent.
InetAddress address = InetAddress.getByName(parsedUri.getHost());
if (address.isAnyLocalAddress() || address.isLoopbackAddress() || address.isSiteLocalAddress()) {
    throw new SecurityException("internal destinations are not allowed");
}
```