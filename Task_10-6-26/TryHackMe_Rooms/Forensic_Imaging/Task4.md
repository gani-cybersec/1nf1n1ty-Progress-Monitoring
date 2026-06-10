# Task 4: Integrity Checking

Integrity checking ensures that the forensic image is identical to the original data. Cryptographic hash functions such as **MD5**, **SHA-1**, and **SHA-256** are used to verify that no modifications have occurred during imaging.

Tools like **dc3dd** can generate hashes during acquisition, providing additional assurance and preserving the chain of custody.

## List Block Devices

Use `lsblk -l` to display block devices:

```bash
sudo lsblk -l
```

## Calculate MD5 Hashes

Calculate the MD5 hash of the forensic image:

```bash
sudo md5sum example1.img
```

Calculate the MD5 hash of the original device:

```bash
sudo md5sum /dev/loop11
```

Example Output:

```text
483ca14c7524b8667974a922662b87e8  example1.img
483ca14c7524b8667974a922662b87e8  /dev/loop11
```

Since both hashes match, the integrity check is successful, confirming that the forensic image is an exact copy of the original device.

## Question

**What is the MD5 hash of `exercise.img` located in `/home/analyst/`?**

**Answer**

```text
1f1da616156f73083521478c334841bb
```


