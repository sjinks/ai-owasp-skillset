# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Object-Level Ownership Check

```python
# Unsafe: record lookup trusts the user-supplied identifier alone.
invoice = Invoice.get(id=request.args["invoice_id"])

# Safer: scope the lookup to the authenticated principal or tenant.
invoice = Invoice.get(id=request.args["invoice_id"], tenant_id=current_user.tenant_id)
```

## Client-Side-Only Enforcement

```javascript
// Unsafe: hiding an admin button does not enforce authorization.
if (user.role !== "admin") {
  deleteButton.hidden = true;
}
```

## Deny-by-Default Policy

```java
// Safer: access is denied unless a policy explicitly grants it.
if (!authorizationService.mayEdit(currentUser, document)) {
    throw new AccessDeniedException();
}
```

## Tenant Boundary Smell

```sql
-- Risky: query returns global data with no tenant filter.
SELECT * FROM invoices WHERE id = ?;
```