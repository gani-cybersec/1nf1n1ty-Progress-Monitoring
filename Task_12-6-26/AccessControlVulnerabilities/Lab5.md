# Lab: User ID Controlled by Request Parameter

## Objective

Exploit the horizontal privilege escalation vulnerability to obtain the API key for the user `carlos` and submit it as the solution.

## Vulnerability

The application uses a user-controllable request parameter to determine which user's account information is displayed. Since the value of the `id` parameter is not properly validated, an attacker can modify it to access the data of other users with the same privilege level, resulting in a horizontal privilege escalation vulnerability.

## Steps

1. Log in using the provided credentials:

```text id="kntmjlwm"
Username: wiener
Password: peter
```

2. Navigate to the account page.
3. Observe that the URL contains the username in the `id` parameter.

### Original Request

```http id="ylfpch8r"
GET /my-account?id=wiener HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
Cookie: session=<session-cookie>
```

4. Send the request to **Burp Repeater**.
5. Modify the value of the `id` parameter from `wiener` to `carlos`.

### Modified Request

```http id="hxhww4q5"
GET /my-account?id=carlos HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
Cookie: session=<session-cookie>
```

6. Send the modified request.
7. Observe that the response contains the account details and API key belonging to `carlos`.
8. Copy the API key and submit it to solve the lab.



The API key for the user `carlos` is successfully retrieved, demonstrating that the application fails to enforce proper access controls on the `id` parameter.

## Payload Used

```text id="pcw57c8d"
id=carlos
```
