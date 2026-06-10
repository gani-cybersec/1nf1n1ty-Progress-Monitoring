## Clickjacking

Clickjacking (UI Redressing) is a web attack in which an attacker tricks a user into clicking on something different from what they perceive. This is achieved by placing a transparent or hidden webpage over a legitimate-looking webpage, causing the victim's clicks to be redirected to the hidden page.

As a result, the victim unknowingly performs actions such as changing account settings, making purchases, transferring money, or deleting an account.


## How Clickjacking Works

The attacker creates a malicious webpage containing attractive content such as a button or image.

A legitimate website is loaded inside an invisible or transparent iframe.

Sensitive buttons on the legitimate website are carefully aligned beneath the visible content.

The victim visits the malicious webpage.

When the victim clicks on the visible content, the click is actually delivered to the hidden webpage.

An unintended action is performed without the victim's knowledge.
