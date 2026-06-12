# Lab: User Role Controlled by Request Parameter

## Objective

Access the administrative panel and delete the user `carlos`.

## Vulnerability

The application determines whether a user is an administrator based on the value of a client-side cookie. Since this cookie can be modified by the user, an attacker can forge administrative privileges and gain unauthorized access to the admin panel.

## Steps

1. Open the lab and attempt to access the `/admin` page.
2. Observe that access to the admin panel is denied.
3. Navigate to the login page and log in using the following credentials:

```text id="bh55zc"
Username: wiener
Password: peter
```

4. In **Burp Suite**, enable **Intercept** and configure response interception.
5. Submit the login request and intercept the server's response.
6. Observe that the response sets the following cookie:

### Original Cookie

```http id="cf0j91"
Set-Cookie: Admin=false
```

7. Modify the cookie value from `false` to `true`.

### Modified Cookie

```http id="kvcf5v"
Set-Cookie: Admin=true
```

8. Forward the response and reload the application.
9. Navigate to the `/admin` page.
10. Access the administrative panel and locate the user `carlos`.
11. Click the **Delete** option to remove the user.



The user `carlos` is successfully deleted, demonstrating that the application relies on a user-controllable cookie to determine administrative privileges.

## Payload Used

```text id="f2fivq"
Admin=true
```
