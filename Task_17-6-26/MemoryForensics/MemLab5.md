# MemLabs Lab 5 – Black Tuesday Write-Up

## Challenge Description

We received this memory dump from our client recently. Someone accessed his system when he was not there and he found some rather strange files being accessed. Their names were composed of alphabets and numbers, making them difficult to recognize. In addition, his favorite application crashed every time he ran it.

The challenge consists of three flags.

---

## Step 1: Identify the Memory Profile

First, I determined the appropriate profile for the memory image using the `imageinfo` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab5.raw imageinfo
```

From the output, the suggested profile was:

```text
Win7SP1x64
```

Therefore, I used this profile for the rest of the analysis.

---

## Step 2: Enumerate Running Processes

I listed the active processes using the `pslist` plugin.

```bash
python2 vol.py -f ../MemoryDump_Lab5.raw --profile=Win7SP1x64 pslist
```

Among the processes, I noticed an interesting entry:

```text
NOTEPAD.EXE
```

Since the challenge description mentioned that the user's favorite application crashed repeatedly, I decided to investigate it further.

---

## Step 3: Examine Command Line Arguments

I used the `cmdline` plugin to inspect the command line arguments of the running processes.

```bash
python2 vol.py -f ../MemoryDump_Lab5.raw --profile=Win7SP1x64 cmdline
```

The output showed:

```text
NOTEPAD.EXE pid: 2724
Command line : "C:\Users\SmartNet\Videos\NOTEPAD.EXE"
C:\Users\SmartNet\Documents\SW1wb3J0YW50.rar
```

The filename `SW1wb3J0YW50.rar` appeared suspicious and became the next target for analysis.

---

## Step 4: Locate and Extract the RAR Archive

I searched for the archive using the `filescan` plugin:

```bash
python2 vol.py -f ../MemoryDump_Lab5.raw --profile=Win7SP1x64 filescan | grep -i SW1wb3J0YW50.rar
```

After obtaining the physical offset, I extracted the file using `dumpfiles`:

```bash
python2 vol.py -f ../MemoryDump_Lab5.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000003eed56f0 -D output
```

The extracted file:

```text
file.None.0xfffffa80010b44f0.dat
```

was renamed to:

```text
lab5.rar
```

---

## Step 5: Extract the Archive and Recover Stage 1 Flag

I extracted the archive:

```bash
unrar x lab5.rar
```

During extraction, a password was required for `Stage2.png`. The archive itself contained the first flag:

### Flag 1

```text
flag{!!_w3LL_d0n3_St4g3-1_0f_L4B_5_D0n3_!!}
```

This flag served as the password for extracting the contents of the archive.

---

## Step 6: Recover Stage 2 Flag

Using the Stage 1 flag as the password, I successfully extracted `Stage2.png`.

After opening the image, I obtained the second flag:

### Flag 2

```text
flag{W1th_th1s_$taGe_2_1s_c0mPL3T3_!!}
```

At first glance, it appeared that the challenge had been completed, but according to the description there was still one more flag remaining.

---

## Step 7: Investigate the Suspicious NOTEPAD.EXE

Since the challenge mentioned that the user's favorite application crashed every time it was executed, I investigated the suspicious executable.

Using `filescan`, I searched for `NOTEPAD.EXE`:

```bash
python2 vol.py -f ../MemoryDump_Lab5.raw --profile=Win7SP1x64 filescan | grep -i notepad.exe
```

The output showed:

```text
0x000000003ee9d070     10      0 R--r-- \Device\HarddiskVolume2\Users\SmartNet\Videos\NOTEPAD.EXE
0x000000003faaf440     17      1 R--r-d \Device\HarddiskVolume2\Windows\System32\en-US\notepad.exe.mui
0x000000003fb8b440     17      1 R--r-d \Device\HarddiskVolume2\Windows\System32\en-US\notepad.exe.mui
0x000000003fca5250      9      0 R--r-d \Device\HarddiskVolume2\Users\SmartNet\Videos\NOTEPAD.EXE
0x000000003fda1d00      3      0 R--r-d \Device\HarddiskVolume2\Windows\System32\notepad.exe
```

The copy located under the user's Videos directory appeared suspicious.

---

## Step 8: Dump the Malicious Executable

I extracted the suspicious executable:

```bash
python2 vol.py -f ../MemoryDump_Lab5.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000003ee9d070 -D output
```

The output showed:

```text
ImageSectionObject 0x3ee9d070
DataSectionObject 0x3ee9d070
SharedCacheMap 0x3ee9d070
```

I extracted the first file corresponding to the `ImageSectionObject`.

---

## Step 9: Reverse Engineer the Binary

I loaded the extracted executable into IDA Pro for analysis.

Upon examining the binary, I found the hidden string corresponding to the third flag.

### Flag 3

```text
L4B_5_D0n3!!
```

**Note:** The challenge description mentions that if the string `L4B_3_D0n3!!` is encountered, it should be corrected to `L4B_5_D0n3!!`.

---

## Conclusion

This challenge demonstrated several memory forensic techniques, including:

* Process enumeration with `pslist`
* Command-line analysis with `cmdline`
* File discovery using `filescan`
* File extraction using `dumpfiles`
* Archive analysis
* Reverse engineering a malicious executable using IDA Pro

### Final Flags

**Flag 1**

```text
flag{!!_w3LL_d0n3_St4g3-1_0f_L4B_5_D0n3_!!}
```

**Flag 2**

```text
flag{W1th_th1s_$taGe_2_1s_c0mPL3T3_!!}
```

**Flag 3**

```text
L4B_5_D0n3!!
```
