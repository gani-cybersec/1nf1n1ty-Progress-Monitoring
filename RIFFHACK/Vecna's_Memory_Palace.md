# Vecna's Memory Palace

**Category:** Forensics
**Difficulty:** Hard
**Platform:** Biterra (RiffHack CTF 2026)

## Challenge Description

> *After a Hawkins Lab blackout, responders recovered a corrupted memory snapshot. Find the final token to close the incident.*

The challenge provided a file named:

```text
memory_snapshot.bin
```

and an Incident Validation page requesting:

* `artifact_name`
* `sha1`
* `unlock_token`

---

## Initial Triage

We first examined the file:

```bash
file memory_snapshot.bin
```

Output:

```text
memory_snapshot.bin: ASCII text
```

Although described as a memory snapshot, it was actually a small text file, making memory forensics tools unnecessary.

Inspecting its contents revealed several entries including:

```text
noise_0
cache_shadow[0]
memfrag[0]
...
analyst_note=fragment metadata survived even when the slab order did not
```

The note suggested that the fragments were out of order and needed reconstruction.

---

## Recovering the Fragments

Each `memfrag[]` entry contained Base64 data.

Example:

```bash
echo "UklEPTR8UE9TPTR8U0laRT01M3xCTE9CPTcxNjA3..." | base64 -d
```

Output:

```text
RID=4|POS=4|SIZE=53|BLOB=71607d797b6b7d72322f...
```

Each fragment contained:

* **RID** → Fragment ID
* **POS** → Actual position
* **SIZE** → Length
* **BLOB** → Hex data

After decoding all fragments, we found:

```text
RID=4 POS=4
RID=1 POS=3
RID=5 POS=2
RID=0 POS=1
RID=2 POS=5
RID=3 POS=0
```

showing that the fragments were intentionally shuffled.

---

## Reassembling the Payload

The fragments were sorted according to the `POS` field and concatenated.

```python
full_hex = ''.join(frags[i] for i in sorted(frags))
raw = bytes.fromhex(full_hex)
```

Result:

```text
318 hex characters
159 bytes
```

Direct decoding produced unreadable output, indicating encryption.

---

## Recovering the XOR Key

The challenge repeatedly referenced Hawkins-related clues:

```text
HAWKINS_LAB_VOLATILE_CAPTURE
mindflayer\resident.sys
```

and mentioned that a "Hawkins-flavored clue" was important.

Trying:

```python
key = b"Hawkins"
```

produced nearly correct output, suggesting the right word but incorrect capitalization.

Using a known plaintext approach revealed the correct key:

```python
key = b"HAWKINS"
```

---

## Decrypting the Payload

Using repeating-key XOR:

```python
out = bytes(
    b ^ key[i % len(key)]
    for i, b in enumerate(raw)
)
```

revealed:

```json
{
  "artifact_name":"mindflayer_loader.dll",
  "campaign":"starcourt_nightshift",
  "sha1":"7f9c2ba4e88f827d616045507605853ed73b809a",
  "unlock_token":"SCOOPS-AHOY-1985"
}
```

---

## Submitting the Incident

The portal required:

| Field         | Value                                    |
| ------------- | ---------------------------------------- |
| artifact_name | mindflayer_loader.dll                    |
| sha1          | 7f9c2ba4e88f827d616045507605853ed73b809a |
| unlock_token  | SCOOPS-AHOY-1985                         |

Submitting these values solved the challenge.

---

## Attack Flow

```text
memory_snapshot.bin
        ↓
Identify memfrag[] entries
        ↓
Base64 Decode
        ↓
Recover RID, POS and BLOB
        ↓
Sort fragments by POS
        ↓
Concatenate hex data
        ↓
Hex Decode
        ↓
159-byte blob
        ↓
Repeating-Key XOR
        ↓
Key = HAWKINS
        ↓
Decrypt JSON
        ↓
Extract artifact_name, sha1 and unlock_token
        ↓
Submit values
        ↓
Challenge Solved
```

## Key Concepts

* Fragment reassembly using the `POS` field.
* Repeating-key XOR encryption.
* Importance of challenge hints in recovering the key.

---

## Flag

```text
bitctf{v3cn45_m3m0ry_p414c3_br34ch}
```
