# Lab: DOM-Based Open Redirection

## Description

The application contains a DOM-based open redirection vulnerability in the "Back to Blog" link. The JavaScript extracts the value of the `url` parameter from the current URL and assigns it directly to `location.href`, allowing an attacker to redirect users to an arbitrary website.

## Vulnerable Code

```javascript id="36n0oh"
returnUrl = /url=(https?:\/\/.+)/.exec(location);

if (returnUrl)
    location.href = returnUrl[1];
else
    location.href = "/";
```

## Steps

1. Inspected the "Back to Blog" link on a blog post page.

2. Observed that the JavaScript extracts the value of the `url` parameter using a regular expression.

3. Noticed that the extracted value is assigned directly to `location.href`.

4. Constructed the following URL (replace `YOUR-LAB-ID` and `YOUR-EXPLOIT-SERVER-ID` accordingly):

```text id="1dzf90"
https://YOUR-LAB-ID.web-security-academy.net/post?postId=4&url=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/
```

5. Visited the URL and clicked the **Back to Blog** link.

6. The browser redirected to the exploit server, solving the lab.

## Explanation

* The application reads the current URL.
* The regular expression extracts the value of the `url` parameter.
* Since the parameter is attacker-controlled, an arbitrary URL can be supplied.
* The extracted value is assigned to:

```javascript id="okcckk"
location.href
```

* As a result, clicking the link redirects the victim to an attacker-controlled website.

## Source and Sink

* **Source:** URL parameter (`url`)
* **Sink:** `location.href`
* **Impact:** DOM-Based Open Redirection

## Exploit URL

```text id="1rj81i"
https://YOUR-LAB-ID.web-security-academy.net/post?postId=4&url=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/
```
