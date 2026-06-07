# Memory Forensics

Memory forensics is the process of analyzing a system's RAM (Random Access Memory) to recover valuable information such as running processes, network connections, user activity, and malware artifacts.

Since RAM is volatile, its contents are lost when the system is powered off, making memory acquisition an important step during an investigation.

## Basic Workflow

A common memory forensic workflow is:

1. Run `strings` for quick clues.
2. Identify the memory profile.
3. List running processes.
4. Investigate suspicious processes.
5. Dump process memory for further analysis.

## Profile Identification

Before analyzing a memory image with Volatility, the correct profile must be identified.

```bash id="m9zwic"
python vol.py -f image.raw imageinfo
```

The `imageinfo` command suggests the operating system profile required for further analysis.

## Process Analysis

Running processes can be viewed using:

```bash id="vq6nwl"
python vol.py -f image.raw --profile=PROFILE pslist
python vol.py -f image.raw --profile=PROFILE pstree
python vol.py -f image.raw --profile=PROFILE psscan
```

These commands help identify suspicious or hidden processes.

## Process Memory Dump

Memory from a specific process can be extracted for analysis.

```bash id="58emrv"
python vol.py -f image.raw --profile=PROFILE memdump -p PID -D dump/
```

This can reveal documents, credentials, malware artifacts, or other useful evidence.

## Useful Volatility Commands

| Command     | Purpose                   |
| ----------- | ------------------------- |
| imageinfo   | Identify memory profile   |
| pslist      | List running processes    |
| pstree      | View process hierarchy    |
| psscan      | Scan for hidden processes |
| memdump     | Dump process memory       |
| connections | View network connections  |
| cmdscan     | Recover command history   |


Memory forensics helps investigators analyze volatile memory to uncover running processes, network activity, user actions, and malware that may not be present on disk.
