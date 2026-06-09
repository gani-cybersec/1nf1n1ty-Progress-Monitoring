# Lab: CSRF Where Token Is Tied to a Non-Session Cookie

## Objective

The objective of this lab is to exploit a Cross-Site Request Forgery (CSRF) vulnerability in the email change functionality. Although the application uses CSRF tokens, they are tied to a separate cookie rather than to the user's session, allowing the protection mechanism to be bypassed.

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

The application uses two cookies:

* `session` – used for authentication.
* `csrfKey` – used for CSRF protection.

The CSRF token is associated with the `csrfKey` cookie instead of the user's session. Consequently, a valid `csrfKey` cookie and its corresponding token can be reused across different user sessions.

---

## Steps Performed

### 1. Capture the Email Change Request

Log in to the application and submit the email update form.

Intercept the request and note the values of:

* `csrfKey` cookie
* `csrf` parameter

---

### 2. Obtain a Second User Session

Open an incognito browser window and log in using the second account.

Capture a fresh email update request.

---

### 3. Replace the Token and Cookie

Use the `csrfKey` cookie and corresponding `csrf` token obtained from the first account in the second account's request.

Since the application validates the token against the `csrfKey` cookie rather than the session, the request is accepted.

---

### 4. Identify Cookie Injection

Perform a search and observe that the search term is reflected in the `Set-Cookie` response header.

This behavior allows arbitrary cookie injection.

---

### 5. Craft the Cookie Injection URL

Create the following URL:

```text
/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None
```

Replace `YOUR-KEY` with the attacker's `csrfKey` value.

---

## Method 1: Generate the CSRF PoC Using Burp Suite Professional

If using Burp Suite Professional:

1. Right-click the email change request.
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

## Method 2: Modify the Generated HTML

Remove the default auto-submit script:

```html
<script>
    document.forms[0].submit();
</script>
```

and replace it with:

```html
<img src="https://https://0a1900f004abfdff81ec93fc00970045.web-security-academy.net/my-account/change-email/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None"
     onerror="document.forms[0].submit()">
```

The exploit contains:

* The email change form.
* The attacker's CSRF token.
* An image used to inject the attacker's `csrfKey` cookie.
* An `onerror` handler that submits the form after the cookie has been injected.

---


---

## Why the Attack Works

The application binds the CSRF token to the `csrfKey` cookie instead of the authenticated session. By injecting the attacker's `csrfKey` cookie into the victim's browser and supplying the corresponding token, the attacker is able to satisfy the CSRF validation and perform actions on behalf of the victim.

