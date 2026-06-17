# Server-Side Request Forgery (SSRF) - Overview

## What is SSRF?

Server-Side Request Forgery (SSRF) is a vulnerability in which an attacker tricks a vulnerable application into making HTTP requests to arbitrary destinations chosen by the attacker.

Instead of the attacker directly accessing a target system, the vulnerable server sends the request on the attacker's behalf.

---

## Basic Working

Normal Flow:

Attacker → Web Application → Legitimate Backend Service

SSRF Flow:

Attacker → Vulnerable Web Application → Internal Server / External Server

The attacker manipulates a user-controllable URL parameter, causing the server to communicate with unintended resources.

---

## Common SSRF Targets

### Localhost

* http://localhost
* http://127.0.0.1
* http://127.1

### Internal Networks

* 192.168.x.x
* 10.x.x.x
* 172.16.x.x – 172.31.x.x

### Cloud Metadata Services

* http://169.254.169.254/latest/meta-data/

---

## Impact of SSRF

SSRF can lead to:

* Accessing internal services.
* Bypassing firewalls and access controls.
* Accessing administrative interfaces.
* Internal network scanning.
* Cloud credential theft.
* Information disclosure.
* Remote Code Execution (RCE) in some cases.

---

## Why SSRF is Dangerous

Applications often trust requests originating from:

* localhost (127.0.0.1)
* Internal IP addresses
* Other backend services

This trust relationship allows attackers to abuse the server as a proxy to reach otherwise inaccessible systems.

---

## Types of SSRF

### 1. Basic SSRF

The response from the backend request is returned to the attacker.

Example:

stockApi=http://localhost/admin

---

### 2. Blind SSRF

The server performs the request, but the response is not returned to the attacker.

Detection is usually performed using Out-of-Band Application Security Testing (OAST) tools such as Burp Collaborator.

---

## Common SSRF Defenses

Applications may implement:

### Blacklist Filters

Blocking:

* localhost
* 127.0.0.1
* /admin

### Whitelist Filters

Allowing only trusted domains.

Example:

https://stock.weliketoshop.net

---

## Bypassing Blacklist Filters

### Alternative IP Representations

* 127.0.0.1
* 127.1
* 2130706433
* 017700000001

### URL Encoding

/admin → %61dmin

### Double URL Encoding

/admin → %2561dmin

### DNS Rebinding

Attacker-controlled domains resolving to localhost.

### Open Redirects

Using trusted URLs that redirect to internal resources.

---

## Bypassing Whitelist Filters

### Userinfo (@)

https://trusted.com@evil.com

Actual host:

evil.com

---

### Fragment (#)

https://evil.com#trusted.com

Actual host:

evil.com

---

### DNS Hierarchy

https://trusted.com.evil.com

Actual host:

trusted.com.evil.com

---

### URL Encoding and Double Encoding

Used to confuse URL parsers and filters.

---

## Blind SSRF Detection

Blind SSRF is commonly detected using OAST techniques.

Example:

stockApi=http://abc123.burpcollaborator.net

If the external server receives a request from the application, SSRF exists.

---

## Open Redirect + SSRF

An attacker can exploit an open redirect vulnerability on a trusted domain to bypass SSRF filters.

Flow:

Attacker
↓
Trusted URL
↓
302 Redirect
↓
Internal Target

Example:

stockApi=http://weliketoshop.net/product/nextProduct?currentProductId=6&path=http://192.168.0.68/admin

The application validates the trusted domain, follows the redirect, and finally accesses the internal server.

---

## Key Takeaway

SSRF occurs when an attacker can control a URL that is used by the server to make HTTP requests. By abusing trust relationships and bypassing filters, attackers can access internal resources that are normally unreachable from the Internet.
