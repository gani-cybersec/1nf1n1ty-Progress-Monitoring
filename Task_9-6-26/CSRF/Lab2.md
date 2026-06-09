# Lab: CSRF Where Token Validation Depends on Request Method

## Objective

The objective of this lab is to exploit a Cross-Site Request Forgery (CSRF) vulnerability in the email change functionality. Although the application implements CSRF protection, the defense is only applied to certain request methods, allowing the protection to be bypassed.

---

## Credentials

* **Username:** `wiener`
* **Password:** `peter`

---

## Vulnerability Analysis

The email change functionality uses a CSRF token to protect POST requests. However, if the request method is changed from POST to GET, the server no longer validates the CSRF token.

As a result, an attacker can convert the request into a GET request and perform a CSRF attack without requiring a valid token.

---

## Steps Performed

### 1. Log in to the Application

Using Burp's browser, log in with the provided credentials:

* Username: `wiener`
* Password: `peter`

---

### 2. Capture the Email Change Request

Navigate to **My Account** and update the email address.

Intercept the request using Burp Suite. The request contains a CSRF token.

---

### 3. Verify CSRF Protection

Send the request to Burp Repeater and modify the value of the `csrf` parameter.

Observe that the request is rejected, confirming that the application validates the CSRF token for POST requests.

---

### 4. Convert the Request Method

In Burp Repeater, right-click the request and select:

```text id="trv6ax"
Change Request Method
```

Convert the request from POST to GET.

Observe that the request succeeds even without a valid CSRF token, indicating that token validation depends on the request method.

---

## Method 1: Generate the GET CSRF PoC Using Burp Suite Professional

If using Burp Suite Professional:

1. Right-click the GET request.
2. Select:

```text id="mwf5w0"
Engagement Tools → Generate CSRF PoC
```

3. Enable:

```text id="i7vkql"
Include auto-submit script
```

4. Click **Regenerate**.

Burp automatically generates a Proof of Concept (PoC) for the GET request.

---

## Method 2: Modify the POST PoC Manually

Instead of generating a new PoC, the POST-based HTML can be modified into a GET-based exploit by removing the `method="POST"` attribute.

The resulting HTML is:

```html id="jlwmir"
<html>
<body>
<script>history.pushState('', '', '/')</script>

<form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="anything@web-security-academy.net" />
    <input type="submit" value="Submit request" />
</form>

<script>
    document.forms[0].submit();
</script>

</body>
</html>
```

Since no `method="POST"` attribute is specified, the browser uses the default GET method.

When the form is automatically submitted, the browser creates a request similar to:

```http id="azb5ke"
GET /my-account/change-email?email=anything@web-security-academy.net
```

The browser then automatically attaches the session cookie, allowing the request to be processed as an authenticated request.

---

## Upload the Exploit

* Navigate to the exploit server.
* Paste the HTML payload into the **Body** section.
* Click **Store**.

---

## Verify the Exploit

Click **View Exploit** to test the payload.

When the page loads, the JavaScript automatically submits the form and sends a GET request to the email change functionality. The browser automatically includes the session cookie, causing the application to process the request as if it originated from the authenticated user.

If the email address changes successfully, the exploit is confirmed to be working.

---

## Deliver the Exploit

Modify the email address so that it does not match the one used during testing.

Click **Deliver to Victim**. When the victim visits the exploit page, their browser automatically submits the forged request, changing the victim's email address and solving the lab.

---

## Why the Attack Works

The application validates the CSRF token only for POST requests. By changing the request method to GET, the server skips CSRF token verification and processes the request based solely on the session cookie, making the application vulnerable to CSRF.

---


This lab demonstrates that implementing CSRF protection only for specific request methods is insufficient. Since the application failed to validate CSRF tokens for GET requests, an attacker was able to bypass the protection and perform unauthorized actions on behalf of an authenticated user.
