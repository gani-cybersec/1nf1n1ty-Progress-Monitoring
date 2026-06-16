# MemLabs Lab 2 - A New World Write-Up

## Challenge Description

One of the clients of our company lost access to his system due to an unknown error. During the investigation, we were informed that the user frequently used browsers and password managers. Our objective was to analyze the memory dump and recover the important information.

**Note:** This challenge consists of **three flags**.

---

# Step 1: Identify the Memory Profile

Using the `imageinfo` plugin, the following profile was identified:

```bash
python2 vol.py -f ../MemoryDump_Lab2.raw imageinfo
```

Suggested profile:

```text
Win7SP1x64
```

All further analysis was performed using this profile.

---

# Step 2: Recover Flag 1 from Environment Variables

The `envars` plugin was used to inspect environment variables.

```bash
python2 vol.py -f ../MemoryDump_Lab2.raw --profile=Win7SP1x64 envars
```

Interesting output:

```text
588 svchost.exe 0x00000000002e1320 NEW_TMP
C:\Windows\ZmxhZ3t3M2xjMG0zX1QwXyRUNGczXyFfT2ZfTDRCXzJ9
```

The string appeared to be Base64 encoded.

Decoding:

```bash
echo "ZmxhZ3t3M2xjMG0zX1QwXyRUNGczXyFfT2ZfTDRCXzJ9" | base64 -d
```

Output:

```text
flag{w3lc0m3_T0_$T4g3_!_Of_L4B_2}
```

### Flag 1

```text
flag{w3lc0m3_T0_$T4g3_!_Of_L4B_2}
```

---

# Step 3: Identify Suspicious Processes

Process enumeration using:

```bash
python2 vol.py -f ../MemoryDump_Lab2.raw --profile=Win7SP1x64 pslist
```

revealed two interesting processes:

* KeePass.exe
* chrome.exe

Since the challenge description mentioned password managers and browsers, these processes became the focus of the investigation.

---

# Step 4: Investigate KeePass

The `cmdline` plugin revealed the file opened by KeePass:

```bash
python2 vol.py -f ../MemoryDump_Lab2.raw --profile=Win7SP1x64 cmdline
```

Output:

```text
"C:\Users\SmartNet\Secrets\Hidden.kdbx"
```

---

## About KeePass

KeePass is an open-source password manager that stores credentials inside an encrypted database file with the extension `.kdbx`. Accessing the database requires a master password.

---

# Step 5: Locate and Recover the KeePass Database

Searching for the database using `filescan`:

```bash
python2 vol.py -f ../MemoryDump_Lab2.raw --profile=Win7SP1x64 filescan | grep -i "hidden.kdbx"
```

Output:

```text
0x000000003fb112a0 \Device\HarddiskVolume2\Users\SmartNet\Secrets\Hidden.kdbx
```

The file was recovered using:

```bash
python2 vol.py -f ../MemoryDump_Lab2.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000003fb112a0 -D output
```

The extracted file was renamed to:

```text
keepass2eww.kdbx
```

However, the database required a password.

---

# Step 6: Recover the KeePass Password

Searching image files:

```bash
python2 vol.py -f ../MemoryDump_Lab2.raw --profile=Win7SP1x64 filescan | grep -i png
```

revealed:

```text
0x000000003fce1c70 \Device\HarddiskVolume2\Users\Alissa Simpson\Pictures\Password.png
```

The image was recovered using `dumpfiles` and opened with:

```bash
eog Password.png
```

The image contained:

```text
p4SSw0rd_123
```

This password successfully unlocked the KeePass database.

---

# Step 7: Recover Flag 2

Opening the database revealed the second flag:

```text
flag{w0w_th1s_1s_Th3_SeC0nD_ST4g3_!!}
```

### Flag 2

```text
flag{w0w_th1s_1s_Th3_SeC0nD_ST4g3_!!}
```

---

# Step 8: Analyze Chrome Browser History

Since Chrome was one of the suspicious processes, the Chrome history plugin was used.

```bash
volatility --plugins=../volatility-plugins/ \
-f MemoryDump_Lab2.raw \
--profile=Win7SP1x64 chromehistory
```

Among the browsing history entries, one URL stood out:

```text
https://mega.nz/#F!TrgSQQTS!H0ZrUzF0B-ZKNM3y9E76lg
```

This indicated that a file had been downloaded from Mega.

---

# Step 9: Download the Archive

Following the link led to an archive named:

```text
Important.zip
```

Examining the archive comment revealed:

```text
Password is SHA1(stage-3-FLAG) from Lab-1. Password is in lowercase.
```

The third-stage flag from Lab 1 was:

```text
flag{w1ll_y0u_3v3r_g3t_this_one}
```

Calculating its SHA1 hash:

```bash
echo -n "flag{w1ll_y0u_3v3r_g3t_this_one}" | sha1sum
```

Output:

```text
6045dd90029719a039fd2d2ebcca718439dd100a
```

---

# Step 10: Extract Important.zip

Using 7-Zip:

```bash
7z x Important.zip
```

Password:

```text
6045dd90029719a039fd2d2ebcca718439dd100a
```

Extraction produced:

```text
Important.png
```

Opening the image:

```bash
eog Important.png
```

revealed the final flag.

---

# Flag 3

```text
flag{Ok_So_Now_St4g3_3_is_DoNE!!}
```

---

# Final Flags

### Flag 1

```text
flag{w3lc0m3_T0_$T4g3_!_Of_L4B_2}
```

### Flag 2

```text
flag{w0w_th1s_1s_Th3_SeC0nD_ST4g3_!!}
```

### Flag 3

```text
flag{Ok_So_Now_St4g3_3_is_DoNE!!}
```

---

# Plugins Used

* imageinfo
* envars
* pslist
* cmdline
* filescan
* dumpfiles
* chromehistory

---

# Conclusion

This challenge demonstrated how memory forensics can be used to recover hidden artifacts from a system. Environment variables, password managers, browser history, and files residing only in memory were all leveraged to uncover the three flags. The investigation showcased the importance of correlating information obtained from multiple Volatility plugins to reconstruct user activity and recover sensitive information.
