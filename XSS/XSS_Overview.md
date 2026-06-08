# Cross-Site Scripting (XSS)

## Definition

Cross-Site Scripting (XSS) is a web security vulnerability that allows an attacker to inject malicious client-side scripts into a trusted website. These scripts execute in the victim's browser within the context of the vulnerable application, potentially leading to session hijacking, credential theft, website defacement, or other malicious actions.

XSS occurs when an application includes untrusted user input in a web page without proper validation, sanitization, or output encoding.

## Types of XSS

### 1. Reflected XSS

Reflected XSS occurs when user-supplied input is immediately returned by the web application in an HTTP response without proper sanitization. The malicious payload is not stored on the server and is typically delivered through a specially crafted URL.

#### Example Flow

1. Attacker creates a malicious URL containing a JavaScript payload.
2. Victim clicks the URL.
3. The application reflects the payload in the response.
4. The browser executes the script.

#### Impact

* Session hijacking
* Credential theft
* Phishing attacks
* User impersonation

---

### 2. Stored XSS

Stored XSS, also known as Persistent XSS, occurs when malicious input is stored on the server (such as in a database, comment section, or user profile) and later displayed to other users without proper sanitization.

#### Example Flow

1. Attacker submits a malicious script through a form.
2. The application stores the payload in the database.
3. Other users visit the affected page.
4. The script executes automatically in their browsers.

#### Impact

* Account takeover
* Theft of sensitive information
* Large-scale attacks against multiple users
* Persistent compromise of application users

---

### 3. DOM-Based XSS

DOM-Based XSS occurs when client-side JavaScript modifies the Document Object Model (DOM) using untrusted data without proper sanitization. The vulnerability exists entirely in the browser and may not involve server-side processing.

#### Example Flow

1. User-controlled data is read from a source such as the URL.
2. Client-side JavaScript inserts the data into the DOM.
3. Malicious JavaScript executes in the browser.

#### Impact

* Client-side code execution
* Session theft
* Manipulation of page content
* Redirection to malicious websites

## Prevention

* Validate and sanitize all user input.
* Perform context-aware output encoding.
* Use secure JavaScript APIs such as `textContent` instead of `innerHTML`.
* Implement a strong Content Security Policy (CSP).
* Regularly test applications for XSS vulnerabilities.


