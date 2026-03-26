# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Resolver-Level Authorization

```javascript
// Unsafe: resolver returns objects by ID with no ownership or tenant check.
const resolvers = {
  Query: {
    invoice: (_, { id }) => Invoice.findByPk(id),
  },
};
```

## Introspection Exposure

```javascript
// Risky: introspection remains enabled in production without restriction.
const server = new ApolloServer({ schema, introspection: true });
```

## Query Cost Guard

```typescript
// Safer: reject queries that exceed a bounded execution budget.
validationRules: [depthLimit(8), createComplexityRule(5000)]
```

## Downstream Resolver Sink

```python
# Risky: resolver arguments reach a downstream SQL sink unsafely.
query = f"SELECT * FROM users WHERE email = '{args['email']}'"
```