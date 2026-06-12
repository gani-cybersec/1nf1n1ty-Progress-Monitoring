## Challenge : FindAndOpen
To slove this lab first find hidden key in the "trace file "and then use the key to unlock "file" which contain the flag

## Steps to slove the challenge



## Step 1: Identify the File Type

First, determine the type of the provided file using the `file` command:

```bash
file dump.pcap
```

The output indicated that `dump.pcap` is a packet capture (PCAP) file containing network traffic.

---

## Step 2: Analyze the Packet Capture

Open the packet capture file in Wireshark:

```bash
wireshark dump.pcap
```

Inspect the packets and follow the relevant streams to locate suspicious data hidden inside the packet contents.

---

## Step 3: Decode the Extracted Data

The data found in the packets was encoded. To decode it, CyberChef was used.

1. Apply the **From Hex** operation.
2. Apply the **From Base64** operation.

After performing both decoding steps, the following key was recovered:

```text
picoCTF{R34DING_LOKd_
```

This key was required to unlock the protected file.

---

## Step 4: Unlock the File

Using the recovered key, the encrypted file was successfully unlocked. After extracting the contents, the complete flag was obtained:

```text
picoCTF{R34DING_LOKd_fil56_succ3ss_c2e6d949}
```

---


   

