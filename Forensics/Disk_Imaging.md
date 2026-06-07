# Disk Imaging

Disk imaging is the process of creating an exact bit-by-bit copy of a storage device, such as a hard drive, SSD, or USB drive. The copy, known as a disk image, contains all files, deleted data, partitions, and filesystem information from the original device.

## Why Is It Important?

In digital forensics, investigators analyze the disk image instead of the original device to preserve evidence and prevent accidental modifications.

## Creating a Disk Image

```bash id="ppn62r"
dd if=/dev/sda of=disk.img
```

## Checksums

A checksum (hash value) is a unique digital fingerprint of a file. Investigators generate hashes before and after imaging to verify that the forensic image is identical to the original disk.

Common hashing algorithms:

| Algorithm | Description                       |
| --------- | --------------------------------- |
| MD5       | Fast but less secure              |
| SHA-1     | More reliable than MD5            |
| SHA-256   | Commonly used in modern forensics |

Example:

```bash id="nqfopg"
md5sum disk.img
sha256sum disk.img
```

## Verifying Integrity

The hash value of the original disk should match the hash value of the disk image. Matching hashes confirm that the evidence has not been altered during acquisition.

## Common Image Formats

| Format          | Description                  |
| --------------- | ---------------------------- |
| RAW (.dd, .img) | Exact sector-by-sector copy  |
| E01             | EnCase forensic image format |
| AFF             | Advanced Forensic Format     |

Disk imaging is a fundamental forensic process used to create an exact copy of a storage device while preserving the integrity of the original evidence. Checksums help ensure the acquired image remains authentic and unchanged.
