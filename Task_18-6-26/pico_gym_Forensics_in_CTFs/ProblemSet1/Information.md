# CyLab Security Academy – Information Challenge Write-Up

## Challenge Description

Files can always be changed in a secret way. Can you find the flag?

**File Provided:** `cat.jpg`

### Hints

* Look at the details of the file.
* Submit the flag in the format `picoCTF{XXXXX}`.

---

## Initial Analysis

After downloading the file, the first step was to verify its type using the `file` command:

```bash
file cat.jpg
```

Output:

```text
cat.jpg: JPEG image data
```

The output confirmed that the file was a JPEG image.

---

## Metadata Analysis

Since the hint suggested examining the file details, I inspected the image metadata using **ExifTool**:

```bash
exiftool cat.jpg
```

While reviewing the metadata fields, I noticed that the **License** field contained a suspicious Base64-encoded string:

```text
License : cGljb0NURnt0aGVfbTN0YWRhdGFfaXNfbW9kaWZpZWR9
```

---

## Decoding the Base64 String

To reveal the hidden message, I decoded the string using the `base64` command:

```bash
echo "cGljb0NURnt0aGVfbTN0YWRhdGFfaXNfbW9kaWZpZWR9" | base64 -d
```

Output:

```text
picoCTF{the_m3tadata_1s_modified}
```

---

## Flag

```text
picoCTF{the_m3tadata_1s_modified}
```

---

