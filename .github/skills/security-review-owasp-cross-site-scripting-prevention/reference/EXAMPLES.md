# Reference Examples

Use these examples only when a short pattern match would help confirm whether a control is safe, unsafe, or incomplete.

## Unsafe HTML Sink vs Safe Text Sink

```javascript
// Unsafe: attacker-controlled HTML is parsed and executed.
element.innerHTML = userInput;

// Safer: attacker-controlled data is treated as text.
element.textContent = userInput;
```

## Framework Escape Hatch

```jsx
// Unsafe: raw HTML rendering without sanitization.
return <div dangerouslySetInnerHTML={{ __html: reviewBody }} />;

// Safer: sanitize first, then render only the sanitized result.
const sanitizedReviewBody = DOMPurify.sanitize(reviewBody);
return <div dangerouslySetInnerHTML={{ __html: sanitizedReviewBody }} />;
```

## URL-Based Script Execution

```javascript
// Unsafe: user-controlled protocols can reach javascript: or data: payloads.
link.setAttribute("href", profileUrl);

// Safer: allow only expected protocols and reject everything else.
const parsedUrl = new URL(profileUrl, "https://example.com");
if (parsedUrl.protocol === "https:") {
  link.setAttribute("href", parsedUrl.toString());
}
```

## Dangerous Script Context Interpolation

```html
<!-- Unsafe: user data lands directly in executable JavaScript. -->
<script>
  const displayName = "{{ userInput }}";
</script>

<!-- Safer: keep untrusted data out of executable script contexts. -->
<div id="display-name"></div>
<script>
  document.getElementById("display-name").textContent = displayNameFromServer;
</script>
```