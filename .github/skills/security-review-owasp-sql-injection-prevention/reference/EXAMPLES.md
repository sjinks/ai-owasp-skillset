# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## String Concatenation vs Parameter Binding

```python
# Unsafe: attacker-controlled input changes SQL structure.
query = "SELECT * FROM users WHERE email = '" + email + "'"
cursor.execute(query)

# Safer: bind values as parameters.
cursor.execute("SELECT * FROM users WHERE email = %s", (email,))
```

## Structural Query Elements

```javascript
// Unsafe: column names and sort direction are attacker-controlled.
const sql = `SELECT * FROM orders ORDER BY ${req.query.sort} ${req.query.direction}`;

// Safer: map user choices to a fixed allowlist.
const sortFields = { created: "created_at", total: "order_total" };
const direction = req.query.direction === "asc" ? "ASC" : "DESC";
const sql = `SELECT * FROM orders ORDER BY ${sortFields[req.query.sort] || "created_at"} ${direction}`;
```

## Dynamic SQL in Stored Procedures

```sql
-- Unsafe: untrusted input becomes executable SQL.
SET @sql = 'SELECT * FROM users WHERE name = ''' + @name + '''';
EXEC(@sql);
```

## Privilege Boundary Smell

```yaml
# Risky: the application connects with an owner-like account.
database:
  username: app_owner
```