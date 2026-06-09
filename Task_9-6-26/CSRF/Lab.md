# Lab: CSRF Where Token Is Not Tied to User Session

## Objective

The objective of this lab is to exploit a Cross-Site Request Forgery (CSRF) vulnerability in the email change functionality. Although the application uses CSRF tokens, they are not tied to the user's session, allowing a token obtained from one account to be used in requests made by another user.

---

## Credentials

### Account 1

* **Username:** `wiener`
* **Password:** `peter`

### Account 2

* **Username:** `carlos`
* **Password:** `montoya`

---

## Vulnerability Analysis

The application uses CSRF tokens to protect the email change functionality. However, the server does not associate the token with a specific user session.

As a result, a valid token obtained from one account can be supplied to another user's request, allowing an attacker to perform a CSRF attack.

---

## Steps Performed

### 1. Obtain a Valid CSRF Token

Log in to one account and submit the email update form.

Intercept the request and note the value of the CSRF token.

---

### 2. Obtain a Request From the Second Account

Open an incognito browser window and log in using the second account.

Capture the email change request.

---

### 3. Use the Token From the First Account

Replace the CSRF token in the second account's request with the token obtained from the first account.

Since the application does not bind tokens to user sessions, the request is accepted.

---

## Method 1: Generate the CSRF PoC Using Burp Suite Professional

If using Burp Suite Professional:

1. Right-click the modified request.
2. Select:

```text
Engagement Tools → Generate CSRF PoC
```

3. Enable:

```text
Include auto-submit script
```

4. Click **Regenerate**.

Burp automatically generates the Proof of Concept.

---

## Method 2: Craft the HTML Manually

Alternatively, the following HTML can be used:

```html
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="anything@web-security-academy.net">
    <input type="hidden" name="csrf" value="ATTACKER_TOKEN">
</form>

<script>
    document.forms[0].submit();
</script>
```

The value of `ATTACKER_TOKEN` should be replaced with a fresh CSRF token obtained from the attacker's account, since the tokens are single-use.

When the victim visits the page, the browser automatically submits the form and includes the victim's session cookie. Because the server only checks whether the token is valid and does not verify that it belongs to the victim's session, the request is processed successfully.

---


## Why the Attack Works

The application verifies that the CSRF token is valid but fails to associate the token with a specific user session. Consequently, a token obtained from the attacker's account can be used in a request sent by the victim's browser, allowing the CSRF protection to be bypassed.

