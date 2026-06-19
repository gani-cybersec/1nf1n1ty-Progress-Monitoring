# CyLab Security Academy – Disk, disk, sleuth! Write-Up

## Challenge Description

This challenge requires using **The Sleuth Kit (TSK)** and Linux command-line techniques to locate a hidden flag inside a provided disk image.

**File Provided:** `dds1-alpine.flag.img.gz`

---

## Step 1: Determine the File Type

First, verify the type of the provided file using the `file` command:

```bash
file dds1-alpine.flag.img.gz
```

Output:

```text
dds1-alpine.flag.img.gz: gzip compressed data
```

The output indicates that the file is compressed using the **gzip** format.

---

## Step 2: Decompress the Disk Image

Extract the disk image using `gunzip`:

```bash
gunzip dds1-alpine.flag.img.gz
```

This produces the file:

```text
dds1-alpine.flag.img
```

> **Note:** If you encounter a "No space left on device" error while extracting the image in your home directory, move the compressed file to `/tmp` and extract it there:

```bash
cp dds1-alpine.flag.img.gz /tmp
cd /tmp
gunzip dds1-alpine.flag.img.gz
```

---

## Step 3: Search the Disk Image

The challenge hint suggests using **srch_strings**, a utility from **The Sleuth Kit** that extracts printable strings from files and disk images.

Run:

```bash
srch_strings dds1-alpine.flag.img
```

Since the output contains a large number of strings, pipe the output to `grep` to search specifically for the flag:

```bash
srch_strings dds1-alpine.flag.img | grep picoCTF
```

Output:

```text
SAY picoCTF{f0r3ns1c4t0r_n30phyt3_5e56e786}
```

This reveals the hidden flag.

---

## Flag

```text
picoCTF{f0r3ns1c4t0r_n30phyt3_5e56e786}
```

---
