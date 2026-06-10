# Task 3: Creating a Forensic Image

After identifying the target device, the next step is to create a **raw disk image** for analysis. In this room, a local image file with the `.img` extension is created.

## Common Imaging Tools

| Tool                       | Description                                                   |
| -------------------------- | ------------------------------------------------------------- |
| **dd**                     | Standard Unix utility for creating raw disk images.           |
| **dc3dd**                  | Enhanced version of `dd` with logging and hashing features.   |
| **ddrescue**               | Used to recover data from damaged drives.                     |
| **FTK Imager**             | GUI-based forensic imaging tool.                              |
| **Guymager**               | GUI tool supporting multiple image formats and detailed logs. |
| **EWF Tools (ewfacquire)** | Used to create and manage Expert Witness Format (EWF) images. |

## Creating the Image

The room uses **dc3dd**, an enhanced version of `dd`.

### Parameters

* **if** (Input File): Source device to copy.
* **of** (Output File): Destination image file.
* **log**: Saves command output to a log file.

### Command

```bash
sudo dc3dd if=/dev/loop11 of=example1.img log=imaging_loop11.txt
```

Where:

* `/dev/loop11` → Source device.
* `example1.img` → Output image file.
* `imaging_loop11.txt` → Log file containing command output.

## Verifying Image Creation

Check the size of the created image:

```bash
ls -alh example1.img
```

Output:

```text
-rw-r--r-- 1 root root 1.1G example1.img
```

The output confirms that a **1.1 GB forensic image** was successfully created.

