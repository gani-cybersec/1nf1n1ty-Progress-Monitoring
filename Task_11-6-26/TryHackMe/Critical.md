# TryHackMe - Critical

## Task 1: Introduction

### Scenario

* User **Hattori** noticed unusual behavior.
* Several PDFs, including **important_document.pdf**, were encrypted.
* Credentials were suspected to be stolen.
* The **DFIR team** collected a memory dump for investigation.

### Learning Objectives

* Understand memory forensics basics.
* Set up the environment.
* Gather information from the compromised machine.
* Identify suspicious activity.
* Extract and analyze memory artifacts.

### Key Point

Memory forensics helps recover volatile data such as:

* Running processes
* Network connections
* Credentials
* Malware artifacts
* Encryption keys

### Investigation Flow

```text id="n07nvr"
Memory Dump
    ↓
Information Gathering
    ↓
Network Analysis
    ↓
Process Analysis
    ↓
Extract Artifacts
    ↓
Identify Attacker Activity
```

### DFIR

**Digital Forensics and Incident Response**

Responsible for:

* Collecting evidence
* Investigating incidents
* Responding to attacks




# Task 2: Memory Forensics

### Key Points

* **Memory Forensics** analyzes **volatile memory (RAM)** on a compromised system.
* RAM contents are lost after a reboot or shutdown.
* Unlike disk forensics, memory forensics provides information about:

  * Running processes
  * Active applications
  * Execution flow
  * Timeline of attacker actions

### Importance

* Captures a snapshot of the system at a specific moment.
* Helps reconstruct the sequence of events during an incident.

### Phases of Memory Forensics

#### 1. Memory Acquisition

* Copy live memory to a file (**memory dump**).
* Prevent loss of evidence due to reboot.
* Preserve data for analysis.

#### 2. Memory Analysis

* Analyze the acquired memory dump.
* Extract forensic artifacts and suspicious activity.


### Questions

**Q1. What type of memory is analyzed during a forensic memory task?**

**Answer:** RAM

---

**Q2. In which phase will you create a memory dump of the target system?**

**Answer:** Memory Acquisition



# Task 3: Environment & Setup

### Memory Acquisition Tools

| OS      | Tool                |
| ------- | ------------------- |
| Windows | FTK Imager, WinPmem |
| Linux   | LIME                |
| macOS   | osxpmem             |



### Useful Commands
Volatility 3 – Memory analysis framework used to analyze memory dumps.
Display Volatility help menu:

```bash
vol -h
```

List Windows plugins:

```bash
vol windows --help
```

### Important Plugins

| Plugin             | Purpose                             |
| ------------------ | ----------------------------------- |
| `windows.info`     | Show OS and kernel details          |
| `windows.cmdline`  | Show process command-line arguments |
| `windows.filescan` | Scan for file objects               |
| `windows.getsids`  | Display SIDs of processes           |
| `windows.handles`  | List open handles                   |
| `windows.netscan`  | Scan network objects                |
| `windows.netstat`  | Show network connections            |
| `windows.mftscan`  | Scan for Alternate Data Streams     |
| `windows.pslist`   | List processes                      |
| `windows.pstree`   | Show process hierarchy              |

### Questions

**Q1. Which plugin can help us get information about the OS running on the lab machine?**

**Answer:** `windows.info`

---

**Q2. Which tool can help us take a memory dump on a Linux OS?**

**Answer:** `LIME`

---

**Q3. Which command will display the help menu using Volatility?**

```bash
vol -h
```


# Task 4: Gathering Target Information

### Goal

Identify the target system's architecture, OS version, and kernel information.

### Command

```bash id="n9m0ga"
vol -f memdump.mem windows.info
```

### Plugin

* **windows.info** → Displays general information about the OS and memory image.

### Sample Output

```text id="9vjsan"
Kernel Base      0xf9066161c000
Is64Bit          False
Major/Minor      15.19041
KeNumberProcessors 2
SystemTime       2024-02-24 22:52:52
NtSystemRoot     C:\Windows
PE MajorOperatingSystemVersion 10
```

### Information Obtained

* Architecture (32-bit/64-bit)
* Windows version
* Number of processors
* System time
* Kernel base address

### Why Important?

* Confirms the environment being analyzed.
* Helps correlate findings with other investigations.
* Ensures the correct machine is being examined.

### Questions

**Q1. Is the architecture of the machine x64 (64-bit)?**

**Answer:** Y

---

**Q2. What is the version of the Windows OS?**

**Answer:** 10

---

**Q3. What is the base address of the kernel?**

**Answer:**

```text id="hq5d8s"
0xf8066161b000
```
##


# Task 5: Searching for Suspicious Activity

### Goal

Identify suspicious network connections and malicious processes.

---

## Network Analysis

### Command

```bash
vol -f memdump.mem windows.netstat
```

### Plugin

* **windows.netstat** → Displays active network connections.

### Interesting Finding

```text
192.168.182.139:3389 ↔ 192.168.182.150:49253
State: ESTABLISHED
PID: 744
Owner: svchost.exe
Time: 2024-02-24 22:47:52
```

### Observation

* Port **3389** corresponds to **RDP**.
* An established connection may indicate the attacker's initial access.

---

## Process Analysis

### Command

```bash
vol -f memdump.mem windows.pstree
```

### Plugin

* **windows.pstree** → Displays processes in a parent-child hierarchy.

### Example Relationship

```text
services.exe (PID 636)
        ↓
dllhost.exe (PID 2964)
```

### Suspicious Process

```text
critical_updat
      ↓
updater.exe
```

### Findings

| Process        | PID  |
| -------------- | ---- |
| critical_updat | 1648 |
| updater.exe    | 1612 |

* `critical_updat` is not a common Windows process.
* `updater.exe` is its child process.
* Indicates possible malicious activity.

---

## Questions

### Q1. Using `windows.netscan`, what is the destination IP address where a connection is established on port 80?

**Answer:**

```text
192.168.182.128
```

---

### Q2. Which program (owner) is using port 80?

**Answer:**

```text
msedge.exe
```

---

### Q3. What is the PID of the child process of `critical_updat`?

**Answer:**

```text
1612
```

---

### Q4. What is the timestamp for the process with the truncated name `critical_updat`?

**Answer:**

```text
2024-02-24 22:51:50.000000
```

---


## Key Takeaways

### `windows.netstat`

Used to identify:

* Active connections
* Remote IP addresses
* Ports
* Process owners

### `windows.pstree`

Used to:

* View parent-child process relationships.
* Detect unusual process chains.


```
# Task 6: Finding Interesting Data

## Goal

Investigate the suspicious `updater.exe` process and extract useful artifacts from memory.

---

## Locate `updater.exe`

### Command

```bash
vol -f memdump.mem windows.filescan > filescan_out
cat filescan_out | grep updater
```

### Plugin

* `windows.filescan` → Finds file objects stored in memory.

### Finding

```text
C:\Users\user01\Documents\updater.exe
```

---

## Obtain File Metadata

### Command

```bash
vol -f memdump.mem windows.mftscan.MFTScan > mftscan_out
cat mftscan_out | grep updater
```

### Plugin

* `windows.mftscan.MFTScan` → Retrieves file metadata and timestamps.

### Timestamps Available

* Created
* Modified
* Updated
* Accessed

---

## Dump Process Memory

### Command

```bash
vol -f memdump.mem -o . windows.memmap --dump --pid 1612
```

### Plugin

* `windows.memmap` → Dumps the memory pages of a process.

### Output

```text
pid.1612.dmp
```

---

## Extract Strings

### Command

```bash
strings pid.1612.dmp | less
```

### Purpose

Extract human-readable text from the dump.

---

## Interesting Artifacts

### URL Found

```text
http://key.critical-update.com/encKEY.txt
```

### File Accessed

```text
important_document.pdf
```

### Examine HTTP Request

```bash
strings pid.1612.dmp | grep -B 10 -A 10 "http://key.critical-update.com/encKEY.txt"
```

### Findings

**Server:**

```text
SimpleHTTP/0.6 Python/3.10.4
```

**Possible Encryption Key:**

```text
cafebabe
```

---

## Questions

### Q1. Full path and name for `critical_updat`?

**Answer**

```text
C:\Users\user01\Documents\critical_update.exe
```

---

### Q2. Created timestamp of `important_document.pdf`?

**Answer**

```text
2024-02-24 20:39:42.000000
```

---

### Q3. Server used by the attacker?

**Answer**

```text
SimpleHTTP/0.6 Python/3.10.4
```

---

## Key Takeaways

| Plugin                    | Purpose                     |
| ------------------------- | --------------------------- |
| `windows.filescan`        | Locate files in memory      |
| `windows.mftscan.MFTScan` | Retrieve file timestamps    |
| `windows.memmap`          | Dump process memory         |
| `strings`                 | Extract readable text       |
| `grep -B -A`              | Show context around matches |

### Indicators of Compromise (IOCs)

```text
critical_update.exe
updater.exe
important_document.pdf
http://key.critical-update.com/encKEY.txt
cafebabe
SimpleHTTP/0.6 Python/3.10.4
```

---

### Definitions

* **Artifact:** Evidence left behind by a process.
* **IOC (Indicator of Compromise):** Information indicating malicious activity.
* **Memory Dump (.dmp):** Snapshot of a process's memory.
* **MFT (Master File Table):** Stores metadata and timestamps for files on NTFS systems.
# Task 7: Conclusion & Wrapping Up

## Summary

In this room, we learned how to perform basic **memory forensics** using **Volatility 3**.

### Skills Covered

* Gathering target information
* Identifying network connections
* Enumerating and analyzing processes
* Investigating suspicious files
* Extracting artifacts from memory dumps
* Recovering URLs, HTTP requests, and possible encryption keys

---



