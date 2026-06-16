# MemLabs Lab 3 - The Evil's Den Write-Up

## Challenge Description

> A malicious script encrypted a very secret piece of information I had on my system. Can you recover the information for me please?

**Note 1:** The challenge consists of only one flag split into two parts.

**Note 2:** The first half of the flag is required to obtain the second half.

Additional tool required:

```bash
sudo apt install steghide
```

---

# Step 1: Identify the Memory Profile

First, I identified the profile of the memory image using the `imageinfo` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab3.raw imageinfo
```

Suggested profile:

```
Win7SP1x86_23418
```

All subsequent analysis was performed using this profile.

---

# Step 2: Enumerate Running Processes

I listed active processes using the `pslist` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab3.raw --profile=Win7SP1x86_23418 pslist
```

Among the running processes, I noticed:

```
notepad.exe
```

This appeared interesting and became the focus of the investigation.

---

# Step 3: Inspect Process Command Lines

To determine which files were opened by Notepad, I used the `cmdline` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab3.raw --profile=Win7SP1x86_23418 cmdline
```

The output revealed two interesting files:

* `evilscript.py`
* `vip.txt`

These files appeared to be associated with `notepad.exe`.

---

# Step 4: Recover the Files

Using the offsets corresponding to these files, I extracted them from memory with the `dumpfiles` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab3.raw \
--profile=Win7SP1x86_23418 \
dumpfiles \
-Q 0x000000003de1b5f0,0x000000003e727e50 \
-D output
```

This recovered:

* `evilscript.py`
* `vip.txt`

---

# Step 5: Analyze the Malicious Script

Contents of `evilscript.py`:

```python
import sys
import string

def xor(s):
    a = ''.join(chr(ord(i)^3) for i in s)
    return a

def encoder(x):
    return x.encode("base64")

if __name__ == "__main__":
    f = open("C:\\Users\\hello\\Desktop\\vip.txt", "w")
    arr = sys.argv[1]
    arr = encoder(xor(arr))
    f.write(arr)
    f.close()
```

The script performs:

1. XOR encryption with key `3`.
2. Base64 encoding.
3. Stores the output inside `vip.txt`.

Since the script encrypts data, I modified it to perform the reverse operations.

```python
import sys
import string
import base64

def xor(s):
    a = ''.join(chr(ord(i)^3) for i in s)
    return a

def decoder(x):
    return (base64.b64decode(x)).decode()

if __name__ == "__main__":
    f = open("vip.txt", "r")
    arr = f.read()
    arr = xor(decoder(arr))
    print(arr)
    f.close()
```

Running the modified script:

```bash
python3 evilscript.py
```

Produced:

```
inctf{0n3_h4lf
```

This was the first half of the flag.

---

# Step 6: Search for Hidden Images

The challenge description indicated that the first half of the flag would be needed to obtain the second half.

Since the challenge required the use of **steghide**, I searched for image files using the `filescan` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab3.raw \
--profile=Win7SP1x86_23418 \
filescan
```

Among the results, I found:

```
\Device\HarddiskVolume2\Users\hello\Desktop\suspision1.jpeg
```

---

# Step 7: Recover the JPEG File

Using its offset, I extracted the image:

```bash
python2 vol.py -f ../MemoryDump_Lab3.raw --profile=Win7SP1x86_23418 dumpfiles -Q 0x0000000004f34148 -D output
```

The recovered file was renamed:

```bash
mv file.None.0x843fcf38.dat lab3.jpeg
```

---

# Step 8: Extract Hidden Data with Steghide

Since the challenge explicitly mentioned Steghide, I used the first half of the flag as the passphrase:

```bash
steghide --extract -sf lab3.jpeg
```

When prompted, I entered:

```
inctf{0n3_h4lf
```

The hidden file `secret text` was successfully extracted.

Displaying its contents:

```bash
cat "secret text"
```

Output:

```
_1s_n0t_3n0ugh}
```

---

# Final Flag

Combining both parts:

```
inctf{0n3_h4lf_1s_n0t_3n0ugh}
```

---

# Plugins Used

* `imageinfo`
* `pslist`
* `cmdline`
* `dumpfiles`
* `filescan`

---

# Conclusion

This challenge demonstrated how memory forensics can be used to recover malicious scripts and hidden files. By analyzing process command lines and extracting artifacts from memory, it was possible to reverse the encryption mechanism, recover the first half of the flag, and use it as the password for extracting the second half hidden inside a JPEG image using Steghide.
