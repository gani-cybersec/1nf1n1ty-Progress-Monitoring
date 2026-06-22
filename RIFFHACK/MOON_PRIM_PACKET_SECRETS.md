# MOON PRIM PACKET SECRETS – Write-Up

## Challenge Description

We are given a ZIP archive containing two files:

* `moon_chat.pcap`
* `luna_selfie.png`

The goal is to analyze the evidence and recover the hidden flag.

---

## Step 1: Inspecting the Packet Capture

We first opened `moon_chat.pcap` in Wireshark and noticed several fragmented UDP packets.

Inspecting the packet payloads revealed hexadecimal data containing strings such as:

```
DATA01
DATA02
DATA03
...
DATA06
```

Converting the hexadecimal payloads into ASCII reconstructed the following conversation.

---

### DATA01

```
Artemis: That idol selfie felt too heavy. Did you stash the intel?
```

### DATA02

```
Luna: Affirmative. Append the rehearsal diary
```

### DATA03

```
after the MOONSHINE marker.
Artemis: Got it. Carve after
```

### DATA04

```
the sentinel, it's still a ZIP.
Luna: Remember, the
```

### DATA05

```
instructions live only in this capture. Delete after
```

### DATA06

```
listening.
```

Combining all fragments gave us the complete message:

> Artemis: That idol selfie felt too heavy. Did you stash the intel?
>
> Luna: Affirmative. Append the rehearsal diary after the MOONSHINE marker.
>
> Artemis: Got it. Carve after the sentinel, it's still a ZIP.
>
> Luna: Remember, the instructions live only in this capture. Delete after listening.

---

## Step 2: Understanding the Clues

The conversation provided several important hints:

* **MOONSHINE marker**
* **Carve after the sentinel**
* **It's still a ZIP**

Since the archive also contained an image named `luna_selfie.png`, we suspected that additional data had been appended to the PNG file.

---

## Step 3: Examining the Image

First, we verified the file type:

```bash
file luna_selfie.png
```

Output:

```text
PNG image data, 640 x 360, 8-bit/color RGB, non-interlaced
```

The image appeared normal, but hidden data can still exist after the PNG end marker.

---

## Step 4: Running Binwalk

We analyzed the image using Binwalk:

```bash
binwalk luna_selfie.png
```

Output:

```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image
41            0x29            Zlib compressed data
5416          0x1528          Zip archive data
                               name: diary_entry.txt
5721          0x1659          End of Zip archive
```

Binwalk revealed a ZIP archive embedded inside the PNG beginning at offset **5416**.

---

## Step 5: Carving the Hidden ZIP

Using the discovered offset, we extracted the ZIP archive:

```bash
dd if=luna_selfie.png of=hidden.zip bs=1 skip=5416
```

Output:

```
327 bytes copied
```

---

## Step 6: Extracting the Archive

We extracted the hidden ZIP:

```bash
unzip hidden.zip
```

Output:

```
inflating: diary_entry.txt
```

---

## Step 7: Reading the Diary Entry

Finally, we examined the contents:

```bash
cat diary_entry.txt
```

Contents:

```
Dear Luna,

I slipped the rehearsal diary behind the MOONSHINE marker in the selfie,
just like the idol manager asked. Anyone inspecting pixel data without
carving tools should only see a pretty gradient.

Flag: bitctf{m00n_pr15m_p4yl04d}

— Usagi
```

---

## Flag

```
bitctf{m00n_pr15m_p4yl04d}
```

---

