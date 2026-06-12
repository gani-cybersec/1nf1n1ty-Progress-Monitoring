## challenge : Packet Prime
downloade the file analyse it with wireshark to get the flag

## Step to slove the challenge

## Step 1: Identify the File Type

First, determine the type of the provided file using the `file` command:

```bash
file network.pcap
```

The output indicated that `network.pcap` is a packet capture (PCAP) file containing network traffic.

---

## Step 2: Analyze the Packet Capture

Open the packet capture file in Wireshark:

```bash
wireshark network.pcap
```

Inspect the packets and follow the relevant streams to locate suspicious data hidden inside the packet contents.

The extracted data was copied as a Base64 string:

```text
CAAnk85zCAAnrzmfCABFAABwUMJAAEAG0bMKAAIPCgACBL5uIygn7NS3vSaZvIAYAfYYdQAAAQEICo3P6WVo8PHDcCBpIGMgbyBDIFQgRiB7IHAgNCBjIGsgMyA3IF8gNSBoIDQgciBrIF8gYiA5IGQgNSAzIDcgNiA1IH0K
```

---

## Step 3: Decode the Base64 String

Decode the string using the following command:

```bash
echo "CAAnk85zCAAnrzmfCABFAABwUMJAAEAG0bMKAAIPCgACBL5uIygn7NS3vSaZvIAYAfYYdQAAAQEICo3P6WVo8PHDcCBpIGMgbyBDIFQgRiB7IHAgNCBjIGsgMyA3IF8gNSBoIDQgciBrIF8gYiA5IGQgNSAzIDcgNiA1IH0K" | base64 -d
```

The output revealed the flag:

```text
picoCTF{p4ck3t_5h4rk_b9d53765}
```

---

## Flag

```text
picoCTF{p4ck3t_5h4rk_b9d53765}
```

---



