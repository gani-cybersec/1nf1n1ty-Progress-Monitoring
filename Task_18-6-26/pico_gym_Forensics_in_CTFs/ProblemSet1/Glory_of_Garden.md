# CyLab Security Academy – Glory of the Garden 

## Challenge Description

This file contains more than it seems.

**File Provided:** `garden.jpg`

### Hint

* What is a hex editor?

---

## Initial Analysis

After downloading the file, I first verified its type using the `file` command:

```bash
file garden.jpg
```

Output:

```text
garden.jpg: JPEG image data
```

The output confirmed that the file was a valid JPEG image.

---

## Examining the File Contents

Since the hint mentioned a hex editor, I inspected the raw contents of the file using the `xxd` command:

```bash
xxd garden.jpg
```

While examining the hexadecimal output, I noticed some readable ASCII text near the end of the file:

```text
Here is a flag: picoCTF{more_than_m33ts_the_3y398e22a}
```

This indicated that additional data had been appended to the image file, allowing the flag to be recovered.

---

## Alternative Method

Another way to search for hidden text is to extract printable strings from the file using the `strings` command:

```bash
strings garden.jpg
```

Output:

```text
...
Here is a flag: picoCTF{more_than_m33ts_the_3y398e22a}
```

This method quickly reveals any human-readable text embedded inside the file.

---

## Flag

```text
picoCTF{more_than_m33ts_the_3y398e22a}
```

---

