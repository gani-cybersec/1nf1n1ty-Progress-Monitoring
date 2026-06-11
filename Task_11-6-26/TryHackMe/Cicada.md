## What Is Cicada 3301?
Cicada 3301 is one of the most mysterious and sophisticated internet puzzles in history. Starting on January 2, 2012, an anonymous group posted an image on 4chan with the message: "Hello. We are looking for highly intelligent individuals." What followed was a multi-layered chain of cryptographic and steganographic puzzles spanning the internet, real-world locations, and multiple disciplines including number theory, classical literature, and cryptography. The group used tools like outguess, PGP signatures, and book ciphers in the real challenge — all of which appear here.
This TryHackMe room recreates that spirit faithfully, chaining 7 stages together where each answer unlocks the next.



## Task 1 — Download the Files
Objective: Download and unzip the task files.
bash# Unzip the provided archive
unzip cicada3301.zip

# Files extracted:
# 3301.wav
# welcome.jpg
Both files are the starting points for the entire chain. No answer required for this task.


--------------------------------------------------------------------------------------------------------------------------------------------------------------------------
## Task 2 — Analyze the Audio (Spectrogram + QR Code)
## Objective: 
Find the link hidden inside 3301.wav.

Technique: Audio Spectrogram Analysis

Tool: Sonic Visualizer (https://sonicvisualiser.org/)

# What is a Spectrogram?
A spectrogram is a visual representation of the frequency spectrum of a signal over time. Data can be hidden in the high-frequency bands of an audio file that are inaudible to humans but visible when the audio is displayed as a spectrogram image. This is a classic audio steganography technique.
Steps

Step 1 — Install and Launch Sonic Visualizer:

bash# On Linux (AppImage method)

chmod +x SonicVisualiser-4.x-x86_64.AppImage

./SonicVisualiser-4.x-x86_64.AppImage

# Or via package manager

sudo apt install sonic-visualiser

Step 2 — Load the audio file:

File → Open → select 3301.wav

Step 3 — Add spectrogram layer:

Layer → Add Spectrogram

Step 4 — Adjust settings for clarity:


Set Color Scheme to: White on Black

Set Scale to: dBV²

Zoom in toward the high frequency range


Step 5 — Result: A QR code appears with the Cicada 3301 cicada logo in the center.

Step 6 — Scan the QR Code:

Scan using a mobile phone camera app or an online QR decoder (e.g., https://zxing.org/w/decode.jspx).

QR Code Output:

https://pastebin.com/wphPq0Aa

Answer — Task 2: https://pastebin.com/wphPq0Aa


## Task 3 — Decode the Passphrase (Base64 + Vigenère Cipher)
## Objective:

Decrypt the passphrase and key found at the Pastebin URL, then use a cipher to recover the final passphrase.

Step 1 — Visit the Pastebin URL

Navigate to https://pastebin.com/wphPq0Aa

Pastebin Contents:

Passphrase: SG01Ul80X1A0NTVtaHA0NTMh

Key:        Q2ljYWRh

Both values are encoded. The hint confirms they are Base64 encoded.

Step 2 — Decode with Base64

Method A — CLI:

bashecho "SG01Ul80X1A0NTVtaHA0NTMh" | base64 -d

# Output: Hm5R_4_P455mhp453!

echo "Q2ljYWRh" | base64 -d

# Output: Cicada

Method B — CyberChef:

Go to https://cyberchef.org

Input: SG01Ul80X1A0NTVtaHA0NTMh

Operation: From Base64

Output: Hm5R_4_P455mhp453!

Repeat for the key:

Input: Q2ljYWRh

Output: Cicada

Decoded values:

Passphrase (raw): Hm5R_4_P455mhp453!

Key: Cicada

Step 3 — Apply the Vigenère Cipher (Variant Beaufort)

The decoded passphrase is still scrambled — it needs to be passed through a cipher using the key Cicada. The hint in the room refers to this as the "French Diplomat Cipher", which is the Vigenère cipher (named after French cryptologist Blaise de Vigenère).

Importantly, the room requires encoding (not decoding) the passphrase with the key using the Variant Beaufort cipher mode.

Using an online Vigenère tool:

Go to: https://www.dcode.fr/vigenere-cipher

Mode: Variant Beaufort / Encode

Message: Hm5R_4_P455mhp453!

Key: Cicada

Or using CyberChef:

Operation: Vigenère Encode

Key: Cicada

Input: Hm5R_4_P455mhp453!

Final Passphrase: Ju5T_4_P455phr453!

Answers — Task 3:

Decrypted passphrase (raw): Hm5R_4_P455mhp453!

Decrypted key: Cicada

Final passphrase (after cipher): Ju5T_4_P455phr453!

## Task 4 — Gather Metadata (steghide on welcome.jpg)
## Objective:
  
  Use the recovered passphrase with steganography tools to extract the hidden message from welcome.jpg.

Technique: Image Steganography Extraction

Tool: steghide

What is steghide?

steghide is a steganography tool that hides data inside JPEG and BMP images by modifying the least significant bits of the image data in a way that is imperceptible to the human eye. A passphrase protects the hidden content and is required for extraction.

Steps

Step 1 — Install steghide:

bashsudo apt install steghide

Step 2 — Extract hidden data:

bashsteghide extract -sf welcome.jpg

# Prompt: Enter passphrase: Ju5T_4_P455phr453!

# Output: wrote extracted data to "invitation.txt"

Step 3 — Read the extracted file:

bashcat invitation.txt

# Output: https://imgur.com/a/c0ZSZga

This is an invitation URL pointing to an image on Imgur.

Step 4 — Download the image from the URL:

bashwget "https://i.imgur.com/8S8OaQw.jpg" -O 8S8OaQw.jpg

# Note: Download with .jpg extension — the tool requires it

Answer — Task 4 (hidden link): https://imgur.com/a/c0ZSZga


-----------------------------------------------------------------------------------------------------------------------------------------------------------
## Task 5 — Extract the PGP Message (outguess)

Objective: Extract hidden data from the downloaded Imgur image using the tool from the real Cicada 3301 challenge.

Technique: Advanced Image Steganography

Tool: outguess

# What is outguess?
outguess is a universal steganographic tool that modifies redundant bits in JPEG images to hide data while preserving statistical properties of the image, making detection much harder than naive LSB substitution. This was the exact tool used by the real Cicada 3301 group in their original 2012–2014 puzzles. Unlike steghide, outguess does not require a passphrase.

Steps

Step 1 — Install outguess:

bash# Method 1: apt (Kali/Debian)

sudo apt install outguess

# Method 2: Build from source

git clone https://github.com/crorvick/outguess

cd outguess

./configure && make && sudo make install

Step 2 — Extract hidden data from the image:

bashoutguess -r 8S8OaQw.jpg result.txt

# Output:

# Reading 8S8OaQw.jpg....
# Extracting usable bits: 29035 bits
# Steg retrieve: seed: 38, len: 1351

Step 3 — Read the extracted message:

bashcat result.txt

Output of result.txt:

-----BEGIN PGP SIGNED MESSAGE-----

Hash: SHA1

Welcome again.

Here is a book code. To find the book, break this hash:

b6a233fb9b2d8772b636ab581169b58c98bd4b8df25e452911ef75561df649edc8852846e81837136840f3aa453e83d86323082d5b6002a16bc20c1560828348

I:1:6
I:2:15
I:3:26
I:5:4
I:6:15
I:10:26
/
/
I:13:5
I:13:1
I:14:7
I:3:29
I:19:8
I:22:25
/
I:23:-1
I:19:-1
I:2:21
I:5:9
I:24:-2
I:22:1
I:38:1

Good luck.

3301


-----BEGIN PGP SIGNATURE-----

Version: GnuPG v1.4.11 (GNU/Linux)

iQIcBAEBAgAGBQJQ5QoZAAoJEBgfAeV6NQkPf2IQ...

-----END PGP SIGNATURE-----

Key elements extracted:

A SHA-512 hash (labelled "Hash: SHA1" in the header — this is misleading, it's actually SHA-512)

A book cipher with notation I:line:character

A PGP signature confirming authenticity

Answer — Task 5: outguess


-----------------------------------------------------------------------------------------------------------------------------------------------------------
## Task 6 — Crack the Hash & Decode the Book Cipher

# Objective:
Identify and crack the hash to find the book, then use the book cipher to extract a URL.

Part A — Hash Identification

Step 1 — Identify the hash type:

Despite the PGP header saying Hash: SHA1, the hash is actually SHA-512 (it's 128 hex characters long — SHA-1 is only 40).

bash# Verify hash length

echo -n "b6a233fb9b2d8772b636ab581169b58c98bd4b8df25e452911ef75561df649edc8852846e81837136840f3aa453e83d86323082d5b6002a16bc20c1560828348" | wc -c

# Output: 128 characters = SHA-512

# Use hash-identifier tool

hash-identifier

# Input hash → Output: [+] SHA-512

The hash:

b6a233fb9b2d8772b636ab581169b58c98bd4b8df25e452911ef75561df649edc8852846e81837136840f3aa453e83d86323082d5b6002a16bc20c1560828348

Part B — Crack the SHA-512 Hash

Method — Online lookup (rainbow table):

URL: https://md5hashing.net/hash/sha512/b6a233fb9b2d8772...

Navigate to https://md5hashing.net and search for the full SHA-512 hash string.

Cracked result:

http://www.notboring.co/words.txt

This URL points to a text file containing the lines of Liber AL vel Legis (The Book of the Law) by Aleister Crowley — the same book used in the original real-world Cicada 3301 puzzle.

bash# Download the book

wget http://www.notboring.co/words.txt -O liber_al.txt

# Check line count

wc -l liber_al.txt

Part C — Decode the Book Cipher

The book cipher notation format is: I:line:character


I = Chapter I (the whole file is Chapter I)


line = line number in the text

character = character position excluding spaces (positive = from start, negative = from end)

/ = delimiter between words (space in output)


Example walkthrough:

I:1:6  → Line 1, 6th non-space character

Line 1: "the  book  of  the  law"

Chars:   t=1  h=2  e=3  b=4  o=5  o=6 → 'o'... 

Wait → "Had! The  manifestation..."

h=1  a=2  d=3  t=4  h=5  e=6 → 'e'... 

→ Result: 'h'

Cipher decoding Python script:

python#!/usr/bin/env python3
# Book Cipher Decoder — Cicada 3301 Vol:1


with open('liber_al.txt', 'r') as f:

    lines = f.readlines()


# Strip newlines, keep the text

book = [line.rstrip('\n') for line in lines]

# Cipher instructions from outguess output

cipher = [

    (1, 6), (2, 15), (3, 26), (5, 4), (6, 15), (10, 26),
    
    None,  # '/'  = space
    
    None,  # '/'  = space
    
    (13, 5), (13, 1), (14, 7), (3, 29), (19, 8), (22, 25),
    
    None,  # '/'  = space
    
    (23, -1), (19, -1), (2, 21), (5, 9), (24, -2), (22, 1), (38, 1)
]


result = []

for entry in cipher:

    if entry is None:
    
        result.append('/')
        
        continue
    
    line_num, char_pos = entry
    
    line = book[line_num - 1]  # 1-indexed
    
    # Remove spaces
    
    chars_only = [c for c in line if c != ' ']
    
    if char_pos > 0:
    
        result.append(chars_only[char_pos - 1])
  
    else:  # negative index = from end
    
        result.append(chars_only[char_pos])



# Replace '/' with space and join

url = ''.join(c if c != '/' else ' ' for c in result).strip()

print("Decoded:", url)

Decoded URL: https://bit.ly/39pw2NH

Answer — Task 6:


Hash type: SHA-512

Book: Liber AL vel Legis (The Book of the Law) by Aleister Crowley

Cracked hash URL: http://www.notboring.co/words.txt

Decoded URL: https://bit.ly/39pw2NH

## Task 7 — Follow the Final URL

#Objective:
Follow the decoded URL to find the final answer.

Step 1 — Visit the bit.ly URL:

https://bit.ly/39pw2NH

This redirects to:

https://soundcloud.com/user-984804993/the-instar-emergence


Step 2 — The SoundCloud page contains a track titled "The Instar Emergence" — a real piece of music produced and published by the actual Cicada 3301 group as part of their original challenge in 2013.

Final Answer — Task 7: The Instar Emergence
-----------------------------------------------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------------------------------------------------------------------------

## TOOLS


# 1. Audio spectrogram

sudo apt install sonic-visualiser

# GUI: Layer → Add Spectrogram → adjust color/scale → scan QR

# 2. Base64 decode

echo "SG01Ul80X1A0NTVtaHA0NTMh" | base64 -d   # → Hm5R_4_P455mhp453!

echo "Q2ljYWRh" | base64 -d                    # → Cicada

# 3. Vigenère cipher

# Online: https://www.dcode.fr/vigenere-cipher

# Mode: Variant Beaufort Encode | Key: Cicada

# Or: CyberChef → Vigenère Encode

## 4. steghide extraction

sudo apt install steghide

steghide extract -sf welcome.jpg

# Passphrase: Ju5T_4_P455phr453!

# # 5. outguess extraction

sudo apt install outguess

# OR: git clone https://github.com/crorvick/outguess && cd outguess && ./configure && make && sudo make install

outguess -r 8S8OaQw.jpg result.txt

cat result.txt

## 6. Hash identification
h
ash-identifier                          # identify SHA-512

# Online crack: https://md5hashing.net

## 7. Download book

wget http://www.notboring.co/words.txt -O liber_al.txt

# 8. Book cipher decode

python3 book_cipher.py                   # script above
