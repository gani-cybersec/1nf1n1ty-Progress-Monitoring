## Challege : Eavesdrop

## steps to slove the challenge

## Step 1: Identify the File Type

First, determine the type of the provided packet capture file:

```bash
file capture.flag.pcap
```

The output indicated that `capture.flag.pcap` is a packet capture (PCAP) file containing network traffic.

---

## Step 2: Analyze the Packet Capture

Open the packet capture file in Wireshark:

```bash
wireshark capture.flag.pcap
```

According to the hint, the capture contains a chat conversation and a file transfer.

Apply the following filter:

```text
tcp.stream eq 0
```

Then, right-click on one of the packets and select:

```text
Follow → TCP Stream
```

This reconstructs the communication between the hosts and reveals the following conversation:

```text
Hey, how do you decrypt this file again?

You're serious?

Yeah, I'm serious

*sigh* openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123

Ok, great, thanks.

Let's use Discord next time, it's more secure.

C'mon, no one knows we use this program like this!

Whatever.

Hey.

Yeah?

Could you transfer the file to me again?

Oh great. Ok, over 9002?

Yeah, listening.

Sent it

Got it.

You're unbelievable
```

---

## Step 3: Recover the Password

From the chat conversation, the command used to decrypt the file was discovered:

```bash
openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123
```

The password used to decrypt the file is:

```text
supersecretpassword123
```

The conversation also indicated that the encrypted file was transferred over port **9002**.

---

## Step 4: Decrypt the File

Using the recovered password, decrypt the transferred file:

```bash
openssl des3 -d -salt -in file.des3 -out file.txt -k supersecretpassword123
```

cat file

---

## Flag

```text
picoCTF{nc_73115_411_5786acc3}
```

---


