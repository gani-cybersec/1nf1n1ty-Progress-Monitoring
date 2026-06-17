# MemLabs Lab 6 – Write-Up

## Challenge Description

We received this memory dump from the Intelligence Bureau Department. They believe it contains information related to the underworld gangster David Benjamin. The dump was acquired from one of his workers who was arrested by the FBI. According to the FBI, David communicated with his workers through the internet, making browser activity a good place to start.

**Note:** The challenge contains one flag split into two parts.

Flag format:

`inctf{s0me_l33t_Str1ng}`

---

## Step 1: Identify the Memory Profile

First, I determined the correct profile using the `imageinfo` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab6.raw imageinfo
```

The suggested profile was:

```text
Win7SP1x64
```

Therefore, I used this profile throughout the analysis.

---

## Step 2: Enumerate Running Processes

Next, I listed the active processes.

```bash
python2 vol.py -f ../MemoryDump_Lab6.raw --profile=Win7SP1x64 pslist
```

Among the running processes, two entries caught my attention:

* `chrome.exe`
* `WinRAR.exe`

Since the challenge description hinted at internet communication, I decided to investigate Chrome first.

---

## Step 3: Examine Chrome History

Using the ChromeHistory plugin, I extracted the browsing history.

```bash
volatility --plugins=../volatility-plugins -f MemoryDump_Lab6.raw --profile=Win7SP1x64 chromehistory
```

Among the visited URLs, one entry stood out:

```text
https://pastebin.com/RSGSi1hk
```

---

## Step 4: Analyze the Pastebin Entry

Opening the Pastebin page revealed a message:

```text
But David sent the key in mail.

The key is.... :(
```

This indicated that the decryption key for the Mega file had been sent through email.

---

## Step 5: Analyze Screenshots

I extracted the desktop screenshots:

```bash
python2 vol.py -f ../MemoryDump_Lab6.raw --profile=Win7SP1x64 screenshot -D .
```

After opening the screenshots with `eog`, one screenshot revealed:

```text
Mega Drive Key - davidbenjamin939@gmail.com - Gmail
```

Another screenshot showed a Mega download page asking for a decryption key.

---

## Step 6: Recover the Mega Key

By analyzing the browser artifacts, I found the email content containing the key:

```text
THE KEY IS

zyWxCjCYYSEMA-hZe552qWVXiPwa5TecODbjnsscMIU
```

Entering this key into the Mega page successfully downloaded a PNG file.

---

## Step 7: Repair the Corrupted PNG

However, the image could not be opened and produced the error:

```text
zTXt: missing IHDR
```

Examining the PNG header with a hex editor showed that the first chunk type had been altered.

I noticed that the hexadecimal value:

```text
69
```

should actually be:

```text
49
```

After correcting the byte and saving the file, the image became readable.

---

## Step 8: Recover the First Half of the Flag

Opening the repaired image revealed:

```text
inctf{thi5_
CH4LL3Ng3_!
s_g0nn4_b3_
?_
```

Combining the text gives the first part of the flag:

```text
inctf{thi5_CH4LL3Ng3_!s_g0nn4_b3_?_
```

---

## Step 9: Investigate WinRAR

Since the second part of the flag was still missing, I turned my attention to `WinRAR.exe`.

Using `filescan`, I recovered the archive associated with WinRAR. Attempting to extract it required a password.

---

## Step 10: Search Environment Variables

To locate the password, I analyzed the environment variables using the `envars` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab6.raw --profile=Win7SP1x64 envars
```

Among the output, I found:

```text
RAR password

easypeasyvirus
```

Using this password, I successfully extracted the archive and obtained `flag2.png`.

---

## Step 11: Recover the Second Half of the Flag

Opening `flag2.png` revealed:

```text
aN_Am4zINg_!_
i_gU3Ss???_}
```

This corresponds to the second half of the flag.

---

## Final Flag

Combining both parts gives:

```text
inctf{thi5_CH4LL3Ng3_!s_g0nn4_b3_aN_Am4zINg_!_i_gU3Ss???_}
```

---

## Conclusion

This challenge demonstrated several memory forensic techniques, including:

* Process enumeration using `pslist`
* Browser artifact analysis using `chromehistory`
* Screenshot recovery using `screenshot`
* Email artifact analysis
* PNG file repair with a hex editor
* Environment variable analysis using `envars`
* Password recovery and archive extraction

The challenge highlighted how information scattered across browser history, screenshots, memory structures, and environment variables can be combined to reconstruct hidden evidence and recover the complete flag.
