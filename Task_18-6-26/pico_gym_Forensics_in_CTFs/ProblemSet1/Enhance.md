# CyLab Security Academy – Enhance

## Challenge Description

Download the SVG image file and find the hidden flag.

**File Provided:** `drawing.flag.svg`

---

## Initial Analysis

After downloading the file, I first verified its type using the `file` command:

```bash
file drawing.flag.svg
```

Output:

```text
drawing.flag.svg: SVG Scalable Vector Graphics image
```

The output confirmed that the file was an SVG image. Since SVG files are XML-based text files, they can be inspected directly to search for hidden information.

---

## Inspecting the SVG File

To locate any embedded text, I searched the file for `<tspan>` elements, which are commonly used to represent text inside SVG images:

```bash
grep "tspan" drawing.flag.svg
```

Output:

```text
id="text3723"><tspan
  id="tspan3748">p </tspan><tspan
  id="tspan3754">i </tspan><tspan
  id="tspan3756">c </tspan><tspan
  id="tspan3758">o </tspan><tspan
  id="tspan3760">C </tspan><tspan
  id="tspan3762">T </tspan><tspan
  id="tspan3764">F { 3 n h 4 n </tspan><tspan
  id="tspan3752">c 3 d _ 2 4 3 7 4 6 7 5 }</tspan></text>
```

The characters of the flag were distributed across multiple `<tspan>` tags.

---

## Reconstructing the Flag

After removing the spaces and combining the characters, the complete flag was obtained:

```text
picoCTF{3nh4nc3d_24374675}
```

---

## Flag

```text
picoCTF{3nh4nc3d_24374675}
```

---


