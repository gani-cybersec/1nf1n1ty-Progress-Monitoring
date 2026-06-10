# Task 3: Practical Example of Digital Forensics

Everything we do on digital devices leaves traces that can be used during investigations. In this scenario, the kidnappers sent a ransom note and an image, both of which contain valuable metadata.

## Files

```bash
cd /root/Rooms/introdigitalforensics
ls
```

Output:

```text
letter-image.jpg
ransom-letter.doc
ransom-letter.pdf
ransom-letter-2.zip
```

## Document Metadata

Metadata stored in documents can reveal information such as the author, creator, and creation date.

The `pdfinfo` utility can be used to extract metadata from PDF files.

```bash
pdfinfo ransom-letter.pdf
```

### Question

**Find the author of `ransom-letter.pdf`.**

**Answer**

```text
Ann Gree Shepherd
```

---

## Photo EXIF Data

Images contain EXIF metadata, which may include:

* Camera model
* Date and time
* GPS coordinates
* Camera settings

The `exiftool` utility is used to read this information.

```bash
exiftool letter-image.jpg
```

From the output, note the GPS coordinates.

### Finding the Location

1. Copy the GPS coordinates displayed by `exiftool`.
2. Replace **deg** with **°** and remove extra spaces.
3. Open Google Maps.
4. Paste the coordinates into the search bar.
5. Google Maps reveals the location where the image was taken.

### Question

**What is the name of the street?**

**Answer**

```text
Milk Street
```

### Question

**What camera model was used to capture the image?**

**Answer**

```text
Canon EOS R6
```

---

