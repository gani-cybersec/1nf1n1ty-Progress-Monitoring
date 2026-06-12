# Lab: Unprotected Admin Functionality

## Objective

Access the unprotected administrative panel and delete the user `carlos`.

## Vulnerability

The application exposes an administrative interface without enforcing proper authentication or authorization checks. In addition, the location of the admin panel is disclosed in the `robots.txt` file, allowing an attacker to discover and access sensitive functionality.

## Steps

1. Open the lab and append `/robots.txt` to the lab URL.
2. Inspect the contents of the `robots.txt` file.
3. Notice that the `Disallow` directive reveals the path to the admin panel.

### robots.txt

```text
User-agent: *
Disallow: /administrator-panel
```

4. Replace `/robots.txt` in the URL with:

```text
/administrator-panel
```

5. Navigate to the administrative panel.
6. Locate the user `carlos`.
7. Click the **Delete** option for the user.



The user `carlos` is successfully deleted, confirming that the administrative functionality is accessible without any authentication or authorization controls.

## Path Discovered

```text
/administrator-panel
```
