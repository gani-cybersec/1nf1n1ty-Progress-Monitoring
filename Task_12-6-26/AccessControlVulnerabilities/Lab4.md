# Lab: User Role Can Be Modified in User Profile

## Objective

Escalate privileges to gain administrative access and delete the user `carlos`.

## Vulnerability

The application exposes the user's role identifier in the response when updating profile information. Since the role ID is included in a user-controllable JSON request, an attacker can modify this value to elevate their privileges. By changing the `roleid` from a regular user role to the administrator role (`roleid = 2`), unauthorized access to the admin panel can be obtained.

## Steps

1. Log in using the provided credentials:

```text id="1myq5n"
Username: wiener
Password: peter
```

2. Navigate to the account page.
3. Use the email update functionality to change the email address associated with the account.
4. Observe that the response contains the current role ID.

### Response

```json id="wtqlu9"
{
    "username": "wiener",
    "email": "wiener@normal-user.net",
    "roleid": 1
}
```

5. Send the email update request to **Burp Repeater**.
6. Modify the JSON request body by adding `"roleid": 2`.

### Original Request

```json id="8uxh25"
{
    "email": "wiener@normal-user.net"
}
```

### Modified Request

```json id="o3u8l5"
{
    "email": "wiener@normal-user.net",
    "roleid": 2
}
```

7. Resend the request and observe that the response indicates the role ID has been changed to `2`.
8. Browse to the `/admin` page.
9. Access the administrative panel and locate the user `carlos`.
10. Click the **Delete** option to remove the user.


The user `carlos` is successfully deleted, demonstrating that the application allows users to modify their own role identifier and gain administrative privileges.

## Payload Used

```json id="4n6l4w"
{
    "roleid": 2
}
```
