# Torrent Analyze Write-Up


## Step 1: Identify the File Type

First, determine the type of the provided capture file:

```bash id="5f5vii"
file capture.pcap
```

The output indicated that `capture.pcap` is a packet capture (PCAP) file containing network traffic.

---

## Step 2: Analyze the Packet Capture

Open the packet capture file in Wireshark:

```bash id="vwt7n8"
wireshark capture.pcap
```

Since the challenge involves torrent traffic, enable the BitTorrent protocols by navigating to:

```text id="z9my3e"
Analyze → Enabled Protocols
```

Then, enable protocols such as:

* BT-DHT
* BitTorrent

---

## Step 3: Inspect the BitTorrent Traffic

Examine the BitTorrent packets to understand the communication between peers, seeds, and leechers.

According to the hint, the downloaded file ends with:

```text id="e7vyy8"
.iso
```

By analyzing the BitTorrent traffic, the name of the downloaded ISO image was discovered.

---

## Step 4: Recover the Flag


```text id="w7z25f"
ubuntu-19.10-desktop-amd64.iso
```

Therefore, the complete flag is:

```text id="uk0gql"
picoCTF{ubuntu-19.10-desktop-amd64.iso}
```

---

## Flag

```text id="sn7zhv"
picoCTF{ubuntu-19.10-desktop-amd64.iso}
```

---
