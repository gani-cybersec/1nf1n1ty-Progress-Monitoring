# Task 6: Practical Exercise

In this task, a separate VM is used to practice the complete forensic imaging process.

## Access Credentials

| Username  | Password  |
| --------- | --------- |
| practical | forensics |

## Exercise 1: Create a Forensic Image

Create an image of the attached 1 GB loop device and verify its integrity by calculating the MD5 hash.

### Answer

```text id="2xkw0g"
1fab86e499934dda789c9c4aaf27101d
```

---

## Exercise 2: Mount the Image

After creating the image, mount it and inspect its contents.

### Create a Mount Point

```bash id="73n5xy"
sudo mkdir -p /mnt/exercise
```

### Mount the Image

```bash id="itxqso"
sudo mount -o loop exercise.img /mnt/exercise
```

### Verify the Contents

```bash id="9w4j67"
ls /mnt/exercise
```

Read the flag file:

```bash id="jmf5kw"
cat /mnt/exercise/flag.txt
```

### Answer

```text id="ocqv8t"
THM{well-done-imaginggggggg}
```

---

