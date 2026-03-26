# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Strict Script Policy Shape

```http
# Safer: nonce-based script execution with object and base restrictions.
Content-Security-Policy: script-src 'nonce-r4nd0m'; object-src 'none'; base-uri 'none'; frame-ancestors 'none'
```

## Permissive Policy Smell

```http
# Risky: inline script execution and broad wildcards largely preserve XSS impact.
Content-Security-Policy: script-src 'unsafe-inline' 'unsafe-eval' *
```

## Meta-Tag Limitation

```html
<!-- Risky: meta-delivered CSP is weaker than a response header and can be absent on some routes. -->
<meta http-equiv="Content-Security-Policy" content="default-src 'self'">
```

## Per-Response Nonce Use

```html
<!-- Safer: the same unpredictable nonce is attached only to intended scripts in one response. -->
<script nonce="r4nd0m">bootApplication()</script>
```