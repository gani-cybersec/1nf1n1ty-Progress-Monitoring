# Hex Editor

A Hex Editor is a tool used to view and edit the raw binary data of a file. It displays file contents in hexadecimal (Hex) format and character format. Hex editors are commonly used in digital forensics to analyze files, recover corrupted data, and examine file structures.

## Features

* View raw file contents
* Edit binary data
* Analyze file structures
* Search for specific data
* Navigate through large files using the "Go To" feature

## Linux Commands

```bash
xxd file.txt
xxd -r file.hex

hexdump file.txt
hexdump -C file.txt
```

### Command Description

* `xxd file.txt` – Displays the contents of a file in hexadecimal format.
* `xxd -r file.hex` – Converts hexadecimal data back to its original binary format.
* `hexdump file.txt` – Displays the contents of a file in hexadecimal format.
* `hexdump -C file.txt` – Displays both hexadecimal and ASCII representations of the file.


A Hex Editor is an important forensic tool that allows investigators to view and modify data at the byte level. It helps in file analysis, data recovery, and digital forensic investigations.
