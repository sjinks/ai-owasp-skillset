# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Dangerous Recursive Merge

```javascript
// Unsafe: nested attacker input can write into inherited properties.
deepMerge(target, req.body);

// Safer: reject dangerous keys before merge and use hardened merge logic.
assertNoPrototypeKeys(req.body);
safeMerge(target, req.body);
```

## Dangerous Key Paths

```json
{
  "constructor": {
    "prototype": {
      "isAdmin": true
    }
  }
}
```

## Null-Prototype Dictionaries

```javascript
// Safer: dictionaries used for untrusted keys do not inherit from Object.prototype.
const params = Object.create(null);
params[req.query.key] = req.query.value;
```

## Authorization Impact Check

```javascript
// Risky: inherited properties can silently influence authorization logic.
if (user.isAdmin) {
  grantAdminAccess();
}
```