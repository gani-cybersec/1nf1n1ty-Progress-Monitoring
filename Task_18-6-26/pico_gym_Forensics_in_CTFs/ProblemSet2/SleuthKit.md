# The Sleuth Kit (TSK) Commands Cheat Sheet

The Sleuth Kit (TSK) is a collection of command-line tools used in digital forensics for analyzing disk images and file systems. The commands are divided into four major categories:

1. Multimedia Layer Commands
2. Block Layer Commands
3. Metadata Layer Commands
4. Filename Layer Commands

---

# 1. Multimedia Layer Commands

These commands are used to examine partition tables and volumes within a disk image.

## mmstat

### Purpose

Displays information about the partition system used by the disk image.

### Syntax

```bash
mmstat disk.img
```

### Example

```bash
mmstat disk.img
```

### Output

```text
DOS Partition Table
```

### Use Case

Determine whether the image uses MBR, GPT, BSD, or another partition scheme.

---

## mmls

### Purpose

Lists the partitions and their starting and ending sectors.

### Syntax

```bash
mmls disk.img
```

### Example

```bash
mmls disk.img
```

### Output

```text
Slot      Start       End        Length      Description
000:000   2048        204799     202752      Linux (0x83)
```

### Use Case

Identify partitions and determine their offsets before analyzing a file system.

---

## mmcat

### Purpose

Extracts a partition from the disk image.

### Syntax

```bash
mmcat disk.img partition_number > partition.dd
```

### Example

```bash
mmcat disk.img 2 > linux_partition.dd
```

### Use Case

Extract a specific partition for separate analysis.

---

# 2. Block Layer Commands

These commands operate on data blocks within the file system.

## blkstat

### Purpose

Displays information about a specific data block.

### Syntax

```bash
blkstat image.dd block_number
```

### Example

```bash
blkstat image.dd 128
```

### Output

```text
Allocated
File system block: 128
```

### Use Case

Determine whether a block is allocated or unallocated.

---

## blkls

### Purpose

Extracts unallocated blocks from a file system.

### Syntax

```bash
blkls image.dd > unallocated.dd
```

### Example

```bash
blkls image.dd > free_space.dd
```

### Use Case

Recover deleted files and search unallocated space for hidden data.

---

## blkcalc

### Purpose

Converts between physical block addresses and file-system block addresses.

### Syntax

```bash
blkcalc image.dd -u block_number
```

### Example

```bash
blkcalc image.dd -u 500
```

### Use Case

Translate block numbers during forensic investigations.

---

# 3. Metadata Layer Commands

These commands work with metadata structures such as inodes and MFT entries.

## istat

### Purpose

Displays metadata information about an inode or MFT entry.

### Syntax

```bash
istat image.dd inode_number
```

### Example

```bash
istat image.dd 15
```

### Output

```text
Allocated
Owner ID
MAC times
Direct blocks
```

### Use Case

View timestamps, ownership, and block information of a file.

---

## icat

### Purpose

Extracts the contents of a file using its inode number.

### Syntax

```bash
icat image.dd inode_number
```

### Example

```bash
icat image.dd 15 > recovered.txt
```

### Use Case

Recover deleted or existing files when only the inode number is known.

---

## ils

### Purpose

Lists inode numbers in the file system.

### Syntax

```bash
ils image.dd
```

### Example

```bash
ils image.dd
```

### Output

```text
15 Allocated
20 Deleted
25 Allocated
```

### Use Case

Locate deleted files and enumerate inodes.

---

## ifind

### Purpose

Finds the inode associated with a file or block.

### Syntax

Find inode from block:

```bash
ifind -d block_number image.dd
```

Find inode from filename:

```bash
ifind -n filename image.dd
```

### Example

```bash
ifind -d 500 image.dd
```

### Output

```text
Inode: 15
```

### Use Case

Map blocks back to files or determine which inode owns a file.

---

# 4. Filename Layer Commands

These commands work with file and directory names.

## fls

### Purpose

Lists files and directories within a file system.

### Syntax

```bash
fls image.dd
```

Recursive listing:

```bash
fls -r image.dd
```

### Example

```bash
fls -r image.dd
```

### Output

```text
r/r 15: document.txt
d/d 20: Downloads
```

### Use Case

Enumerate files, folders, and deleted entries.

---

## fcat

### Purpose

Displays the contents of a file using its filename.

### Syntax

```bash
fcat image.dd filename
```

### Example

```bash
fcat image.dd document.txt
```

### Use Case

Read file contents directly using the filename.

---

## find

### Purpose

Searches for files and directories.

### Syntax

```bash
find . -name "*.txt"
```

### Example

```bash
find . -name "flag.txt"
```

### Output

```text
./Documents/flag.txt
```

### Use Case

Locate files by name or extension.

---

# Summary Table

| Category   | Command | Purpose                           |
| ---------- | ------- | --------------------------------- |
| Multimedia | mmstat  | Identify partition type           |
| Multimedia | mmls    | List partitions and offsets       |
| Multimedia | mmcat   | Extract a partition               |
| Block      | blkstat | Show block information            |
| Block      | blkls   | Extract unallocated blocks        |
| Block      | blkcalc | Convert block addresses           |
| Metadata   | istat   | Display inode metadata            |
| Metadata   | icat    | Recover file from inode           |
| Metadata   | ils     | List inodes                       |
| Metadata   | ifind   | Find inode from block or filename |
| Filename   | fls     | List files and directories        |
| Filename   | fcat    | Display file contents             |
| Filename   | find    | Search for files                  |

## Investigation Flow

```text
Disk Image
     ↓
mmls
     ↓
Partition Offset
     ↓
fls
     ↓
File/Inode Number
     ↓
istat
     ↓
icat
     ↓
Recovered File
```

This sequence (mmls → fls → istat → icat) is the most commonly used workflow during disk forensic investigations.
