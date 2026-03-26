# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Synchronizer Token Validation

```python
# Unsafe: state-changing form handling has no CSRF token check.
if request.method == "POST":
    transfer_funds(request.form["amount"])

# Safer: reject the request unless the server-issued token matches.
if request.method == "POST":
    validate_csrf_token(request.form["csrf_token"], session["csrf_token"])
    transfer_funds(request.form["amount"])
```

## Double-Submit Cookie With Binding

```javascript
// Risky: matching two attacker-set values is not meaningful protection.
if (req.cookies.csrf === req.body.csrf) {
  processTransfer();
}

// Safer: bind the token to server-side secret material before comparison.
if (verifySignedCsrfToken(req.cookies.csrf, req.body.csrf, session.id)) {
  processTransfer();
}
```

## Origin Verification

```java
// Safer: reject cross-site state-changing requests from unexpected origins.
String origin = request.getHeader("Origin");
if (!"https://app.example.com".equals(origin)) {
    throw new SecurityException("unexpected origin");
}
```

## Client-Side Request Construction

```javascript
// Unsafe: attacker-controlled input chooses the authenticated request target.
fetch(location.hash.slice(1), { method: "POST", credentials: "include" });

// Safer: map user actions to predefined endpoints only.
const routes = { profile: "/api/profile", email: "/api/email" };
fetch(routes[action], { method: "POST", credentials: "include" });
```