# Task 5: Other Types of Imaging

Forensic imaging is not limited to disks. Other types of imaging include:

| Type               | Description                                                              |
| ------------------ | ------------------------------------------------------------------------ |
| **Remote Imaging** | Acquires data over a network without physical access to the device.      |
| **USB Images**     | Creates an image of a USB drive.                                         |
| **Docker Images**  | Creates a snapshot of a Docker container's filesystem and configuration. |

## Mounting an Image

After creating a forensic image, it can be mounted to verify its contents.

### Check Mounted File Systems

```bash
df -h
```

This confirms that the image is not currently mounted.

### Create a Mount Point

```bash
sudo mkdir -p /mnt/example1
```

### Mount the Image

```bash
sudo mount -o loop example1.img /mnt/example1
```

### Verify the Mount

List the contents of the mounted directory:

```bash
ls /mnt/example1
```

This confirms that the image has been mounted successfully and its contents are accessible.

## Question

**Mount the image `exercise.img` located in the analyst home directory. What is the content of `flag.txt`?**

**Answer**

```text
THM{mounttt-mounttt-me}
```

