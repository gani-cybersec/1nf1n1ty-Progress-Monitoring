# Lab: Unprotected Admin Functionality with Unpredictable URL

## Objective

Access the hidden administrative panel and delete the user `carlos`.

## Vulnerability

The application exposes an administrative interface without proper authentication or authorization controls. Although the admin panel is located at an unpredictable URL, its location is disclosed within the application's source code through JavaScript, allowing an attacker to discover and access the administrative functionality.

## Steps

1. Open the lab and navigate to the home page.
2. Review the page source using **Burp Suite** or the browser's developer tools.
3. Observe that the page references a JavaScript file containing code related to the admin panel.
4. Inspect the JavaScript file and identify the following code:

### JavaScript Code

```javascript
var isAdmin = false;
if (isAdmin) {
   var topLinksTag = document.getElementsByClassName("top-links")[0];
   var adminPanelTag = document.createElement('a');
   adminPanelTag.setAttribute('href', '/admin-nfp62m');
   adminPanelTag.innerText = 'Admin panel';
   topLinksTag.append(adminPanelTag);
   var pTag = document.createElement('p');
   pTag.innerText = '|';
   topLinksTag.appendChild(pTag);
}
```

5. Notice that the `href` attribute reveals the location of the admin panel:

```text
/admin-nfp62m
```

6. Navigate to the disclosed URL.
7. Access the admin panel.
8. Locate the user `carlos` and click the **Delete** option.



The user `carlos` is successfully deleted, demonstrating that sensitive administrative functionality is exposed without proper access controls.

## Admin Panel Path

```text
/admin-nfp62m
```
