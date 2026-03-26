# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Parser Hardening in Java

```java
// Unsafe: default parser configuration may allow DTD and external entity handling.
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();

// Safer: explicitly disable dangerous features.
factory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
factory.setFeature("http://xml.org/sax/features/external-general-entities", false);
factory.setFeature("http://xml.org/sax/features/external-parameter-entities", false);
factory.setXIncludeAware(false);
factory.setExpandEntityReferences(false);
```

## Dangerous XML Payload Shape

```xml
<!DOCTYPE invoice [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<invoice><customer>&xxe;</customer></invoice>
```

## Unsafe External Access in Downstream Consumers

```java
// Unsafe: stylesheet or schema resolution can trigger external fetches.
TransformerFactory factory = TransformerFactory.newInstance();

// Safer: disable external DTD and stylesheet access.
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_DTD, "");
factory.setAttribute(XMLConstants.ACCESS_EXTERNAL_STYLESHEET, "");
```