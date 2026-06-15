# MemLabs Lab 0 - Never Too Late Mister

## Description

The challenge provides a memory dump (`Challenge.raw`) and hints that John is an "environmental" activist, hates Thanos, and uses too many variables. The objective is to determine what John was doing and recover the flag.

## Steps

### 1. Identify the Memory Profile

Used the `imageinfo` plugin to determine the appropriate profile for the memory image.

```bash
volatility -f Challenge.raw imageinfo
```

Selected profile:

```text
Win7SP1x86
```

---

### 2. Enumerate Running Processes

Listed active processes using `pslist`.

```bash
volatility -f Challenge.raw --profile=Win7SP1x86 pslist
```

Interesting processes observed:

* `cmd.exe`
* `DumpIt.exe`
* `explorer.exe`

---

### 3. Recover Executed Commands

Used the `cmdscan` plugin to inspect command history.

```bash
volatility -f Challenge.raw --profile=Win7SP1x86 cmdscan
```

Discovered the execution of:

```text
C:\Python27\python.exe C:\Users\hello\Desktop\demon.py.txt
```

---

### 4. Inspect Console Output

Examined console buffers using the `consoles` plugin.

```bash
volatility -f Challenge.raw --profile=Win7SP1x86 consoles
```

Recovered the following hexadecimal string:

```text
335d366f5d6031767631707f
```

Converting from hex produced unreadable data.

---

### 5. Examine Environment Variables

Since the description mentioned "environment", inspected environment variables.

```bash
volatility -f Challenge.raw --profile=Win7SP1x86 envars
```

Found a suspicious variable:

```text
Thanos = xor
password
```

This suggested that the data was XOR encrypted.

---

### 6. Perform XOR Decoding

Decoded the hex string and brute-forced all possible XOR keys.

```python
a = "335d366f5d6031767631707f".decode("hex")

for i in range(0,255):
    b = ""
    for j in a:
        b += chr(ord(j)^i)
    print b
```

One output revealed:

```text
1_4m_b3tt3r}
```

indicating part of the flag.

---

### 7. Extract Password Hashes

Dumped user hashes using:

```bash
volatility -f Challenge.raw --profile=Win7SP1x86 hashdump
```

Recovered the NTLM hash:

```text
101da33f44e92c27835e64322d72e8b7
```

---

### 8. Crack the Hash

Cracking the NTLM hash revealed:

```text
flag{you_are_good_but
```

---

### 9. Combine Both Parts

First part:

```text
flag{you_are_good_but
```

Second part:

```text
1_4m_b3tt3r}
```

Final flag:

```text
flag{you_are_good_but1_4m_b3tt3r}
```

## Flag

```text
flag{you_are_good_but1_4m_b3tt3r}
```
