# Reflected Cross-Site Scripting (XSS)

Reflected Cross-Site Scripting (XSS) occurs when an application receives data from an HTTP request and includes that data directly in the response without proper validation or encoding. As a result, an attacker can inject malicious JavaScript that executes in the victim's browser.

## Example

Suppose a website has a search function that accepts user input through a URL parameter:

```html
https://insecure-website.com/search?term=gift
```

The application displays the search term in the response:

```html
<p>You searched for: gift</p>
```

If the application does not properly sanitize user input, an attacker can inject a script payload:

```html
https://insecure-website.com/search?term=<script>alert(1)</script>
```

The response becomes:

```html
<p>You searched for: <script>alert(1)</script></p>
```

When a user visits the malicious URL, the browser executes the injected JavaScript.

##  Lab: Reflected XSS into HTML context with nothing encode
## Steps

1. Open the search functionality of the application.
2. Enter a test payload:

```html
<script>alert(1)</script>
```

3. Submit the request.
4. Observe that the payload is reflected in the response and executed by the browser.
5. The alert box appears, confirming the presence of a Reflected XSS vulnerability.
6. The lab is successfully solved.

## Impact

* Execution of arbitrary JavaScript.
* Session hijacking.
* Cookie theft.
* User impersonation.
* Phishing attacks.

## Prevention

* Validate user input.
* Encode output before displaying it in HTML.
* Implement Content Security Policy (CSP).
* Use secure coding practices and frameworks.



The application was vulnerable to Reflected Cross-Site Scripting because user input was reflected in the HTTP response without proper sanitization. By injecting a JavaScript payload, arbitrary code execution was achieved in the victim's browser.
