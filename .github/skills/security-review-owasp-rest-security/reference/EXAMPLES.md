# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Method-Level Authorization Gap

```python
# Unsafe: GET checks auth but DELETE reaches the handler with no equivalent guard.
@app.route("/orders/<order_id>", methods=["DELETE"])
def delete_order(order_id):
    return cancel_order(order_id)
```

## Token Verification

```java
// Safer: verify signature, issuer, audience, and lifetime before using claims.
JWTVerifier verifier = JWT.require(algorithm)
    .withIssuer("auth.example")
    .withAudience("orders-api")
    .build();
DecodedJWT token = verifier.verify(rawToken);
```

## Content-Type Confusion

```javascript
// Risky: request body is parsed without enforcing the expected content type.
app.use(express.text({ type: "*/*" }));
```

## Workflow Bypass

```http
# Risky: direct API calls can skip expected business-state transitions.
POST /api/orders/123/ship
```