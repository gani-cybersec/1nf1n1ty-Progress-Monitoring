# Lab: DOM XSS using Web Messages and JSON.parse

## Description

The application listens for web messages and parses the received data using `JSON.parse()`. Based on the `type` property, a switch statement performs different actions. In the `load-channel` case, the value of the `url` property is assigned to the `src` attribute of an iframe, resulting in a DOM-based XSS vulnerability.

## Steps

1. Inspected the home page and identified an event listener handling web messages.

2. Observed that incoming messages are parsed using `JSON.parse()`.

3. Noticed that the switch statement contains a `load-channel` case, which sets the iframe's `src` attribute to the value of the `url` property.

4. On the exploit server, added the following payload (replace `YOUR-LAB-ID` with the actual lab ID):

```html
<iframe src=https://YOUR-LAB-ID.web-security-academy.net/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```

5. Stored the exploit and delivered it to the victim.

## Explanation

* When the iframe loads, `postMessage()` sends a JSON string to the target page.
* The event listener receives the message and parses it using:

```javascript
JSON.parse()
```

* The resulting object contains:

```json
{
  "type": "load-channel",
  "url": "javascript:print()"
}
```

* The switch statement matches the `load-channel` case.

* The application assigns the `url` value to the `src` attribute of `ACMEplayer.element`.

* Since the value is:

```javascript
javascript:print()
```

the browser executes the payload, causing the `print()` function to be called and solving the lab.

## Source and Sink

* **Source:** `event.data` (web message)
* **Sink:** `iframe.src`
* **Impact:** DOM-based Cross-Site Scripting (XSS)

## Payload

```html
<iframe src=https://YOUR-LAB-ID.web-security-academy.net/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```
