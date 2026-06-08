# Stored Cross-Site Scripting (XSS)

Stored Cross-Site Scripting (Stored XSS), also known as Persistent XSS or Second-Order XSS, occurs when an application accepts user input, stores it on the server, and later displays it to other users without proper sanitization or encoding.

As a result, malicious JavaScript supplied by an attacker is permanently stored and executed whenever a victim visits the affected page.

## Example

Suppose a website allows users to post comments on blog articles.

A normal user submits the following comment:

```http
POST /post/comment HTTP/1.1

postId=3&comment=This+post+was+extremely+helpful.&name=Carlos+Montoya
```

The comment is stored in the application's database and displayed to users as:

```html
<p>This post was extremely helpful.</p>
```

If the application does not properly sanitize user input, an attacker can submit a malicious comment:

```html
<script>alert(1)</script>
```

The payload is stored in the database and later displayed as:

```html
<p><script>alert(1)</script></p>
```

When another user visits the page, the browser executes the injected JavaScript.

## Lab :Stored XSS into HTML context with nothing encoded

## Steps

### Step 1: Open a Blog Post

Navigate to any blog post and locate the comment submission form.

### Step 2: Submit a Malicious Payload

Enter the following payload in the comment field:

```html
<script>alert(1)</script>
```

Fill in the required fields such as name and email, then submit the comment.

### Step 3: View the Blog Post

After submitting the comment, reload the blog page.

### Step 4: Observe Payload Execution

The application displays the stored comment without sanitizing it, causing the browser to execute the JavaScript payload and display an alert box.

## Payload Used

```html
<script>alert(1)</script>
```

The application was vulnerable to Stored Cross-Site Scripting because user input submitted through the comment functionality was stored and later rendered without proper sanitization. By injecting a JavaScript payload into a comment, arbitrary code execution was achieved in the browsers of users viewing the affected page.
