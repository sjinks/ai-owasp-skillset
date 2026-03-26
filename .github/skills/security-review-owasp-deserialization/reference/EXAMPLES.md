# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Native Deserialization of Untrusted Data

```python
# Unsafe: pickle can execute attacker-controlled gadget chains.
session_data = pickle.loads(request.cookies["session"])
```

```php
// Unsafe: unserialize on attacker-controlled input.
$profile = unserialize($_POST['profile']);
```

## Safer Data-Only Parsing

```python
# Safer: parse data-only formats into expected primitive structures.
payload = json.loads(request.data)
user_id = int(payload["user_id"])
```

## Type Allowlisting

```java
// Safer: reject unexpected classes before object materialization.
ObjectInputFilter filter = ObjectInputFilter.Config.createFilter(
    "com.example.dto.*;java.base/*;!*"
);
inputStream.setObjectInputFilter(filter);
```

## Dangerous Polymorphic Typing

```java
// Unsafe: attacker-controlled type metadata can select arbitrary classes.
objectMapper.activateDefaultTyping(polymorphicTypeValidator);
```