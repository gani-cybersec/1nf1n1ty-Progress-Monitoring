# Lab: DOM XSS using Web Messages

## Description

The application listens for web messages using `addEventListener()`. The received message is inserted into the page without proper sanitization, leading to a DOM-based XSS vulnerability.

## Steps

1. Inspected the home page and identified that it uses `addEventListener()` to receive web messages.

2. Opened the exploit server and inserted the following payload (replacing `YOUR-LAB-ID` with the actual lab ID):

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```

3. Stored the exploit and delivered it to the victim.

## Explanation

* When the iframe loads, `postMessage()` sends the payload to the target page.
* The page's event listener receives the message and inserts it into the `ads` div using an unsafe HTML sink.
* The injected `<img>` tag contains an invalid `src` value, causing an error.
* The `onerror` event handler executes `print()`, triggering the print dialog and solving the lab.

## Source and Sink

* **Source:** `event.data` (web message)
* **Sink:** `innerHTML`
* **Impact:** DOM-based Cross-Site Scripting (XSS)

## Payload

```html
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```
