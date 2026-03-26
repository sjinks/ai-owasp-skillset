# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Extension Allowlist vs Blocklist

```python
# Unsafe: simple blocklists are easy to bypass.
if not filename.endswith(".php"):
    save_upload(file)

# Safer: accept only a narrow allowlist of required extensions.
allowed_extensions = {".jpg", ".png", ".pdf"}
if pathlib.Path(filename).suffix.lower() in allowed_extensions:
    save_upload(file)
```

## Randomized Server-Side Naming

```javascript
// Risky: user-controlled names can collide, traverse, or expose intent.
const destination = path.join(uploadDir, req.file.originalname);

// Safer: generate a server-side name and keep the original name as metadata only.
const destination = path.join(uploadDir, crypto.randomUUID());
```

## Webroot Exposure

```nginx
# Risky: uploaded content is served directly from a web-accessible path.
location /uploads/ {
    alias /var/www/uploads/;
}

# Safer: store uploads outside the webroot and serve them through an authorization layer.
```

## Archive Extraction

```java
// Unsafe: archive entries can escape the target directory.
Path outputPath = uploadRoot.resolve(entry.getName());

// Safer: normalize and reject entries that leave the intended root.
Path outputPath = uploadRoot.resolve(entry.getName()).normalize();
if (!outputPath.startsWith(uploadRoot)) {
    throw new SecurityException("invalid archive entry path");
}
```