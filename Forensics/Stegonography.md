# Steganography

Steganography is the practice of hiding data inside another file so that the hidden information is not obvious. Images, audio files, and videos are commonly used to conceal data.

## Detecting Steganography

Investigators look for unusual file sizes, modified timestamps, or hidden data embedded within files.

Common tools:

```bash id="4swjlwm"
strings file.jpg
binwalk file.jpg
stegseek file.jpg
```

## LSB Steganography

LSB (Least Significant Bit) Steganography hides data in the last bit of a byte. Since changing the last bit causes only a small change, the hidden data is usually invisible to the human eye.

For example, suppose we want to hide the character **"y"** inside an image.

Convert **y** to binary:

```text id="ylqr5d"
y = 01111001
```

Assume part of the image contains the following bytes:

```text id="szdfvv"
11111111
11111111
11111111
11111111
11111111
11111111
11111111
11111111
```

Replace the least significant bit of each byte with the bits of **01111001**:

| Original Byte | Modified Byte |
| ------------- | ------------- |
| 11111111      | 11111110      |
| 11111111      | 11111111      |
| 11111111      | 11111111      |
| 11111111      | 11111111      |
| 11111111      | 11111110      |
| 11111111      | 11111110      |
| 11111111      | 11111110      |
| 11111111      | 11111111      |

The hidden character is now stored within the image data without noticeably changing the image.

To recover the message, extract the least significant bit from each byte and combine the bits to reconstruct the original data.

