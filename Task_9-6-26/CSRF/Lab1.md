# Lab: CSRF Vulnerability with No Defenses

## Objective

The objective of this lab is to exploit a Cross-Site Request Forgery (CSRF) vulnerability in the email change functionality by crafting a malicious HTML page and delivering it to the victim through the exploit server.

---

## Credentials

* **Username:** `wiener`
* **Password:** `peter`

---

## Vulnerability Analysis

The email change functionality is vulnerable to CSRF because:

* The application uses cookie-based session handling.
* The browser automatically sends the session cookie with requests.
* The request does not contain a CSRF token or any other unpredictable parameter.

As a result, an attacker can forge a request that is processed by the server as if it originated from the authenticated user.

---

## Steps Performed

### 1. Log in to the Application

Using Burp's browser, log in with the provided credentials:

* Username: `wiener`
* Password: `peter`

---

### 2. Capture the Email Change Request

Navigate to **My Account** and change the email address.

Intercept the request using Burp Suite. The request is similar to:

```http
POST /my-account/change-email HTTP/1.1
Host: YOUR-LAB-ID.web-security-academy.net
Cookie: session=...
Content-Type: application/x-www-form-urlencoded

email=test@web-security-academy.net
```

---

### 3. Analyze the Request

The request contains only one parameter:

```http
email=test@web-security-academy.net
```

Since no CSRF token or additional validation mechanism is present, the request can be reproduced from another website.

---

### 4. Generate the CSRF Proof of Concept

Using Burp Suite Professional:

* Right-click the request.
* Select **Engagement Tools → Generate CSRF PoC**.
* Enable **Include auto-submit script**.
* Click **Regenerate**.

Alternatively, the following HTML can be used:

```html
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="anything@web-security-academy.net">
</form>

<script>
    document.forms[0].submit();
</script>
```

--


### 5. Upload the Exploit

* Navigate to the exploit server.
* Paste the HTML payload into the **Body** section.
* Click **Store**.

---

## Verify the Exploit

Click **View Exploit** to test the payload.

When the page loads, the JavaScript automatically submits the form and sends a POST request to the email change functionality. The browser automatically includes the session cookie, causing the application to process the request as if it came from the authenticated user.

If the email address changes successfully, the exploit is confirmed to be working.

---

## Deliver the Exploit

Modify the email address so that it differs from the one used during testing.

Click **Deliver to Victim**. When the victim visits the exploit page, their browser automatically submits the forged request, changing the victim's email address and solving the lab.

---

## Why the Attack Works

The attack succeeds because the application relies solely on session cookies for authentication and does not implement any CSRF protection mechanisms. Consequently, the server treats the forged request as a legitimate request from the authenticated user.

---

## Remediation

This vulnerability can be mitigated by:

* Implementing CSRF tokens.
* Using SameSite cookies.
* Validating the Origin and Referer headers.
* Requiring re-authentication for sensitive actions.

---
