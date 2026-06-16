MemLabs Lab 1 – Beginner's Luck Write-Up
Challenge Description# MemLabs Lab 1 – Beginner's Luck Write-Up

## Challenge Description

My sister's computer crashed, but fortunately we were able to recover the memory dump. According to the challenge description, a black command window suddenly appeared and something was executed. At the same time, she was drawing something. The objective of this challenge is to recover all the important files and obtain the three flags hidden inside the memory image.

---

## Step 1: Identify the Memory Profile

The first step was to determine the correct profile for the memory dump.

```bash
python2 vol.py -f MemoryDump_Lab1.raw imageinfo
```

The suggested profile was:

```text
Win7SP1x64
```

---

## Step 2: Investigating the Black Window

Since the challenge description mentioned a black command window appearing before the crash, the `consoles` plugin was used to inspect the command history.

```bash
python2 vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 consoles
```

Among the output, the following command and Base64-encoded string were discovered:

```text
C:\Users\SmartNet>St4G3$1
ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=
```

The Base64 string was decoded using:

```bash
echo "ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=" | base64 -d
```

Output:

```text
flag{th1s_1s_th3_1st_st4g3!!}
```

### Flag 1

```text
flag{th1s_1s_th3_1st_st4g3!!}
```

---

## Step 3: Investigating the Drawing Activity

The challenge description also stated that the user was drawing something before the crash. Therefore, the running processes were examined.

```bash
python2 vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 pslist
```

Among the running processes, one interesting process stood out:

```text
mspaint.exe    PID 2424
```

Since Microsoft Paint was active, its memory was dumped:

```bash
python2 vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 memdump -p 2424 -D .
```

Output:

```text
Writing mspaint.exe [2424] to 2424.dmp
```

The dump file was renamed:

```bash
mv 2424.dmp 2424.data
```

and opened using GIMP:

```bash
gimp 2424.data
```

Using **Load Image from Raw Data**, adjusting the dimensions, and scrolling through the recovered image revealed handwritten text containing:

```text
flag{Good_Boy_good_girl}
```

### Flag 2

```text
flag{Good_Boy_good_girl}
```

---

## Step 4: Investigating Command-Line Activity

To determine what other files the user had opened before the crash, the `cmdline` plugin was used.

```bash
python2 vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 cmdline
```

One interesting entry appeared:

```text
WinRAR.exe pid: 1512

Command line:
"C:\Program Files\WinRAR\WinRAR.exe"
"C:\Users\Alissa Simpson\Documents\Important.rar"
```

This indicated that the user had recently opened a file named **Important.rar**.

---

## Step 5: Recovering Important.rar

The archive was located using the `filescan` plugin.

```bash
python2 vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 filescan | grep -i "Important.rar"
```

Output:

```text
0x000000003fa3ebc0
```

The file was recovered using `dumpfiles`.

```bash
python2 vol.py -f MemoryDump_Lab1.raw \
--profile=Win7SP1x64 \
dumpfiles \
-Q 0x000000003fa3ebc0 \
-D .
```

Output:

```text
DataSectionObject 0x3fa3ebc0 None \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
```

The recovered file appeared as:

```text
file.None.0xfffffa8001034450.dat
```

Its type was verified using:

```bash
file file.None.0xfffffa8001034450.dat
```

Output:

```text
RAR archive data, v5
```

The file was then renamed:

```bash
mv file.None.0xfffffa8001034450.dat file.rar
```

---

## Step 6: Extracting the Archive

Attempting to extract the archive:

```bash
unrar e file.rar
```

displayed the following hint:

```text
Password is NTLM hash (in uppercase) of Alissa's account password.
```

Therefore, the NTLM hash of Alissa Simpson's account had to be recovered.

---

## Step 7: Recovering User Password Hashes

The `hashdump` plugin was used:

```bash
python2 vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 hashdump
```

Among the recovered hashes was:

```text
Alissa Simpson:1003:
aad3b435b51404eeaad3b435b51404ee:
f4ff64c8baac57d22f22edc681055ba6:::
```

The NTLM hash for Alissa Simpson was:

```text
f4ff64c8baac57d22f22edc681055ba6
```

Since the archive requested the uppercase version, the password became:

```text
F4FF64C8BAAC57D22F22EDC681055BA6
```

---

## Step 8: Recovering the Final Flag

The archive was extracted again:

```bash
unrar e file.rar
```

After supplying the password:

```text
F4FF64C8BAAC57D22F22EDC681055BA6
```

the file:

```text
flag3.png
```

was successfully extracted.

The image was opened using:

```bash
eog flag3.png
```

The image revealed the third flag:

```text
flag{w1ll_3rd_stage_was_easy}
```

### Flag 3

```text
flag{w1ll_3rd_stage_was_easy}
```

---

# Final Flags

### Flag 1

```text
flag{th1s_1s_th3_1st_st4g3!!}
```

### Flag 2

```text
flag{Good_Boy_good_girl}
```

### Flag 3

```text
flag{w1ll_3rd_stage_was_easy}
```

---

# Volatility Plugins Used

* imageinfo
* consoles
* pslist
* memdump
* cmdline
* filescan
* dumpfiles
* hashdump

---



