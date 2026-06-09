# Lab: CSRF Where Token Validation Depends on Token Being Present

## Objective

The objective of this lab is to exploit a Cross-Site Request Forgery (CSRF) vulnerability in the email change functionality. Although the application implements CSRF protection, the server only validates the token when the `csrf` parameter is present. If the parameter is removed entirely, the request is accepted.

---

## Credentials

* **Username:** `wiener`
* **Password:** `peter`

---

## Vulnerability Analysis

The email change functionality includes a CSRF token. However, the server only validates the token if the `csrf` parameter is included in the request.

As a result, removing the `csrf` parameter completely bypasses the protection and allows a CSRF attack to succeed.

---

## Steps Performed

### 1. Log in to the Application

Using Burp's browser, log in with the provided credentials:

* Username: `wiener`
* Password: `peter`

---

### 2. Capture the Email Change Request

Navigate to **My Account** and update the email address.

Intercept the request using Burp Suite. The request contains an email parameter and a CSRF token.

---

### 3. Verify CSRF Protection

Send the request to Burp Repeater and modify the value of the `csrf` parameter.

Observe that the request is rejected, confirming that the application validates the token.

---

### 4. Remove the CSRF Parameter

Delete the entire `csrf` parameter from the request and resend it.

Observe that the request succeeds, indicating that the server only validates the token when the parameter is present.

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
</form>

<script>
    document.forms[0].submit();
</script>
```

Since the `csrf` parameter is omitted, the browser sends a request containing only the email parameter. The session cookie is automatically attached by the browser, causing the request to be processed as an authenticated request.

---

## Upload the Exploit

Navigate to the exploit server, paste the HTML payload into the **Body** section, and click **Store**.

---

## Verify the Exploit

Click **View Exploit** to test the payload. If the email address changes successfully, the exploit is confirmed to be working.

---

## Deliver the Exploit

Change the email address so that it differs from the one used during testing.

Store the exploit and click **Deliver to Victim** to solve the lab.

---

## Why the Attack Works

The application validates the CSRF token only when the `csrf` parameter is present. By removing the parameter entirely, the server skips token validation and processes the request based solely on the session cookie.

---

