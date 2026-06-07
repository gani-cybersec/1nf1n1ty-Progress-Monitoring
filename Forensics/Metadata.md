# Metadata

Metadata is data about data. It provides information about a file, such as when it was created, modified, accessed, who created it, and the software used to generate it. Metadata helps investigators understand the history and context of a file.

## EXIF Data

EXIF (Exchangeable Image File Format) is metadata commonly embedded in image files. It can contain information such as camera model, GPS coordinates, timestamps, image dimensions, and editing software.

```bash
exiftool image.jpg
```

The `exiftool` utility extracts metadata from files and displays information such as file size, timestamps, author details, device information, and EXIF fields.

## MAC Times

MAC times are filesystem timestamps used to track file activity and build forensic timelines.

| Timestamp        | Description                              |
| ---------------- | ---------------------------------------- |
| Modified (mtime) | Last time the file contents were changed |
| Accessed (atime) | Last time the file was opened or read    |
| Created (btime)  | When the file was originally created     |
| Changed (ctime)  | Last time the file metadata was modified |

## MFT Timestamps

On NTFS file systems, the Master File Table (MFT) stores metadata for every file and directory.

| Timestamp              | Location               |
| ---------------------- | ---------------------- |
| Date Changed           | MFT Entry              |
| Filename Date Created  | MFT Filename Attribute |
| Filename Date Modified | MFT Filename Attribute |
| Filename Date Accessed | MFT Filename Attribute |

## INDX Timestamps

NTFS directories maintain index records (`$I30`) that store file references and timestamps. These entries can provide valuable evidence even when files have been moved or deleted.

| Timestamp          | Location          |
| ------------------ | ----------------- |
| INDX Date Created  | NTFS `$I30` Index |
| INDX Date Modified | NTFS `$I30` Index |
| INDX Date Accessed | NTFS `$I30` Index |
| INDX Date Changed  | NTFS `$I30` Index |

## Why It Matters

Metadata and timestamps help investigators reconstruct the sequence of events on a system. By analyzing MAC times, MFT records, and INDX entries, investigators can determine when files were created, modified, accessed, moved, or deleted.

