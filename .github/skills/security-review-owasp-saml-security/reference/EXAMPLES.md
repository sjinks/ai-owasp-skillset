# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Signature Trust Source

```java
// Unsafe: trust is derived from attacker-controlled message content.
KeyInfo keyInfo = signature.getKeyInfo();
validateSignature(signature, keyInfo);

// Safer: validate signatures only against the configured IdP certificate or metadata trust anchor.
validateSignature(signature, configuredIdpCertificate);
```

## Loose XML Element Selection

```java
// Unsafe: broad element selection can support signature-wrapping attacks.
Node assertion = document.getElementsByTagName("Assertion").item(0);
```

## Replay-Relevant Conditions

```xml
<SubjectConfirmationData Recipient="https://sp.example.com/acs"
                         NotOnOrAfter="2026-03-26T10:15:00Z"
                         InResponseTo="_request123" />
```

## RelayState Validation

```python
# Unsafe: unsolicited RelayState values can redirect users after login.
return redirect(request.form["RelayState"])

# Safer: map RelayState to a known allowlist of post-login routes.
return redirect(allowed_destinations[request.form["RelayState"]])
```