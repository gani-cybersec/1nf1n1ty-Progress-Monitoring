# File Formats and File Signatures

## Overview

File extensions alone do not determine a file's type. Files contain unique bytes at the beginning called **file signatures** (or **magic numbers**) that help operating systems and forensic tools identify the actual file format.

## Why Are They Important?

A file can be renamed from `malware.exe` to `photo.jpg`, but its file signature remains unchanged. This helps investigators determine the true file type even when extensions are missing or altered.

## Viewing File Signatures

```bash
xxd file.jpg | head
hexdump file.jpg
```

Example output:

```text
FFD8FFE0 00104A46 494600
```

The signature `FF D8 FF` and the marker `JFIF` indicate that the file is a JPEG image.

## Common File Signatures

| File Type | Hex Signature | ASCII |
| --------- | ------------- | ----- |
| JPEG      | FF D8 FF      | ÿØÿ   |
| PNG       | 89 50 4E 47   | .PNG  |
| PDF       | 25 50 44 46   | %PDF  |
| ZIP       | 50 4B 03 04   | PK..  |
| EXE       | 4D 5A         | MZ    |



File signature analysis is a fundamental forensic technique used to identify a file's true format, regardless of its extension.
