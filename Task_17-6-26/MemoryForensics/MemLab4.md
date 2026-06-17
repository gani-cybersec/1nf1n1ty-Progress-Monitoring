# MemLabs Lab 4 – Obsession Write-Up

## Challenge Description

My system was recently compromised. The hacker stole a lot of information and deleted a very important file. The only evidence available was a memory dump. The objective was to recover the deleted file and obtain the flag.

**Flag format:** `inctf{s0me_l33t_Str1ng}`

---

## Step 1: Identify the Memory Profile

First, I determined the correct profile for the memory image using the `imageinfo` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab4.raw imageinfo
```

From the output, the suggested profile was:

```
Win7SP1x64
```

Therefore, I used this profile for the remainder of the analysis.

---

## Step 2: Enumerate Running Processes

Next, I listed the active processes using the `pslist` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab4.raw --profile=Win7SP1x64 pslist
```

However, nothing suspicious or particularly interesting was found.

---

## Step 3: Analyze Desktop Screenshots

I then used the `screenshot` plugin to capture the desktop contents of the user sessions.

```bash
python2 vol.py -f ../MemoryDump_Lab4.raw --profile=Win7SP1x64 screenshot -D screenshots
```

After opening the screenshots with `eog`, I noticed information related to the users, which provided useful context for further investigation.

---

## Step 4: Search for User Files

Using the `filescan` plugin, I searched for files located in user directories.

```bash
python2 vol.py -f ../MemoryDump_Lab4.raw --profile=Win7SP1x64 filescan | grep -i '\\users\\' | grep -i "jpg\|jpeg\|png\|txt\|pt\|exe\|"
```

Among the results, I discovered a file named:

```
important.txt
```

I attempted to extract the file using `dumpfiles`, but since the file had already been deleted, the extraction was unsuccessful.

---

## Step 5: Parse the Master File Table

Since the file had been deleted, I used the `mftparser` plugin to analyze NTFS metadata stored in memory.

```bash
python2 vol.py -f ../MemoryDump_Lab4.raw --profile=Win7SP1x64 mftparser > mfdata
```

To locate the metadata associated with the deleted file, I searched for `important.txt`:

```bash
cat mfdata | grep -i important.txt -A 30
```

This revealed the resident data attribute of the file containing a hexadecimal dump.

---

## Step 6: Recover the Flag

The hex dump contained ASCII characters separated by `0d0a` newline bytes. After removing the newline characters and reconstructing the string, the contents of the deleted file were recovered.

### Flag

```
inctf{1_is_n0t_EQu4l_7o_2_bUt_th1s_d0s3nt_m4ke_s3ns3}
```

The file also contained the message:

```
Good work :P
```

---

## Conclusion

This challenge demonstrated how deleted files can still leave traces within the NTFS Master File Table. Although `important.txt` could not be recovered directly using `dumpfiles`, analyzing the MFT records with `mftparser` allowed the contents of the deleted file to be reconstructed and the flag to be recovered successfully.


