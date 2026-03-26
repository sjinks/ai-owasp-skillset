# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Direct Model Binding

```javascript
// Unsafe: every request field is copied into the persisted model.
const user = await User.create(req.body);
```

## DTO Boundary

```typescript
// Safer: accept only the public fields, then map explicitly.
type CreateUserInput = {
  displayName: string;
  timezone: string;
};

const input: CreateUserInput = req.body;
const user = new User();
user.displayName = input.displayName;
user.timezone = input.timezone;
```

## Sensitive Field Injection

```json
{
  "email": "attacker@example.com",
  "isAdmin": true,
  "accountStatus": "approved"
}
```

## Allowlist Configuration

```ruby
# Safer: only explicitly allowed attributes are mass-assignable.
params.require(:user).permit(:display_name, :timezone)
```