# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Session ID Regeneration

```javascript
// Unsafe: the pre-auth session survives login unchanged.
req.session.userId = user.id;

// Safer: regenerate the session identifier after authentication.
req.session.regenerate(() => {
  req.session.userId = user.id;
});
```

## Cookie Attributes

```http
# Safer: session cookies are constrained to secure browser handling.
Set-Cookie: session=abc123; Secure; HttpOnly; SameSite=Lax; Path=/
```

## URL-Based Session Tracking

```java
// Unsafe: session identifiers in URLs leak through logs, history, and referrers.
response.sendRedirect("/account;jsessionid=" + session.getId());
```

## Logout Invalidation

```python
# Safer: server-side state is destroyed before the cookie is cleared.
session.clear()
session.invalidate()
response.delete_cookie("session")
```