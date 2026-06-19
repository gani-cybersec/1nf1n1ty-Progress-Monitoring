# PicoCTF – Sleuthkit Intro

## Challenge Description

In this challenge, a compressed disk image (`disk.img.gz`) is provided. The objective is to determine the size of the Linux partition contained within the image and submit the answer to a remote service in order to obtain the flag.

---

## Step 1: Download the Disk Image

First, download the compressed disk image:

```bash
wget https://artifacts.picoctf.net/c/164/disk.img.gz
```

---

## Step 2: Decompress the Image

Since the file is compressed using the Gzip format (`.gz`), it must be decompressed using `gunzip`:

```bash
gunzip disk.img.gz
```

This produces the file: 

```text
disk.img
```

> **Note:** Attempting to use `unzip` will fail because `.gz` files are not ZIP archives.

---

## Step 3: Examine the Partition Table

To inspect the partition layout of the disk image, use the `mmls` utility from **The Sleuth Kit**:

```bash
mmls disk.img
```

Output:

```text
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001  Primary Table (#0)
001:  -------   0000000000   0000002047   0000002048  Unallocated
002:  000:000   0000002048   0000204799   0000202752  Linux (0x83)
```

From the output, the Linux partition is identified by:

```text
Linux (0x83)
```

The **Length** column indicates the size of the partition in sectors:

```text
0000202752
```

Therefore, the size of the Linux partition is:

```text
202752 sectors
```

---

## Step 4: Connect to the Challenge Server

Connect to the remote service using Netcat:

```bash
nc saturn.picoctf.net 59905
```

The service prompts:

```text
What is the size of the Linux partition in the given disk image?
Length in sectors:
```

Enter:

```text
0000202752
```

Upon submitting the correct answer, the server returns the flag.

---

## Flag

```text
picoCTF{mm15_f7w!}
```

---

## Commands Used

### Download the Image

```bash
wget https://artifacts.picoctf.net/c/164/disk.img.gz
```

### Decompress the Image

```bash
gunzip disk.img.gz
```

### Display Partition Layout

```bash
mmls disk.img
```

### Connect to the Checker

```bash
nc saturn.picoctf.net 59905
```

---

## Key Concepts Learned

* **The Sleuth Kit (TSK)** is a collection of digital forensics tools used for analyzing disk images.
* **mmls** displays the partition layout and metadata of a disk image.
* The **Length** column in the `mmls` output represents the partition size in sectors.
* Files with the `.gz` extension must be decompressed using **gunzip**, not `unzip`.
* **Netcat (nc)** can be used to communicate with remote challenge services and submit answers.

This challenge demonstrates the use of disk forensics tools to analyze partition structures and extract information from disk images.
