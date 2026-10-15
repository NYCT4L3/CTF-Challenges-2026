# Dead Air: Writeup

> **Spoilers.** This file contains the full solution.

**Category:** Forensics · **Difficulty:** Medium (a decent player needs about 20–40 minutes)  
**Tools:** Audacity, 7-Zip or `binwalk` or `unzip`, the `file` command, `exiftool` (optional), and a Pigpen cipher chart (for example [dcode.fr/pigpen-cipher](https://www.dcode.fr/pigpen-cipher)).

The file names are deliberately misleading. The audio is named `silence.wav` but is not silent. Inside the zip, the decoy is called `flag.txt` and the real evidence is called `README.txt`, even though it is a PNG image.

## Solution

### 1. Listen to the audio

Playing `silence.wav` gives static, a beep, and then garbled, speech-like sounds that repeat three times.

### 2. Reverse it

Open the file in Audacity, select all, and choose **Effect > Reverse**. The garbled sounds become clear speech: *purple lantern garden*.

The password is the three words joined, all lowercase, with no spaces: `purplelanterngarden`.

### 3. Find the zip hidden after the audio

The zip archive is attached to the end of the WAV file. Any of these finds it:

- **7-Zip:** right-click `silence.wav`, then 7-Zip, then *Open archive*
- **binwalk:** `binwalk -e silence.wav`
- **unzip:** `unzip silence.wav` (it warns about extra bytes at the start, which is normal)

Enter the password `purplelanterngarden`. Inside are two files: `flag.txt` and `README.txt`.

### 4. Ignore the decoy

`flag.txt` contains a fake flag, `CTF{NICE_TRY_THIS_ONE_IS_FAKE}`. It is not accepted.

### 5. Find out what `README.txt` really is

Opening `README.txt` in a text editor shows unreadable characters, but the first characters include `PNG`. Running `file README.txt` confirms it:

```
README.txt: PNG image data, 1238 x 470, 8-bit/color RGB, non-interlaced
```

Rename it to `README.png` and open it. It shows rows of angular symbols with dots.

`exiftool README.txt` also shows a hint in the Comment field: *The Freemasons kept their secrets in squares and dots.*

### 6. Decode the Pigpen cipher

The symbols are the **Pigpen cipher** (also called the Freemason cipher). Each letter is the part of a tic-tac-toe grid or an X that surrounds it, with or without a dot. Use a chart such as dcode.fr/pigpen-cipher. The bigger gaps between symbols separate words, and the picture is wrapped onto three lines.

The words read:

```
i am the pebble that shatters diamonds
```

### 7. Build the flag

Pigpen has no capital letters or underscores, so the player writes the flag in the usual format: words joined by underscores, with the sentence starting with a capital `I`.

**Flag:** `CTF{...}` (redacted; the decoded sentence above, joined with underscores and starting with a capital `I`)

## Why the clues work

- The password exists only as sound. It appears nowhere as text in the file, so `strings` and every text-based shortcut find nothing.
- The password is three ordinary words with no digits, so it is not in common wordlists and there is no "9 versus nine" ambiguity.
- The zip is attached after the audio data, so audio players ignore it and only a file-carving or archive tool shows it.
- The misleading names (`silence.wav`, `flag.txt`, `README.txt`) reward players who check what a file really is.
- The decoy catches players who grab the first thing that looks like a flag.

## Hints

| # | Hint |
|---|---|
| 1 | A file can hold more than it plays. |
| 2 | Some messages are meant to be heard from the other end. |
| 3 | The audio is not alone. Something is attached after the sound ends. |
| 4 | Three words, all lowercase, no spaces. |
| 5 | File names and extensions can lie. Ask the system what a file really is. |
| 6 | Squares, dots and angles: the secret alphabet of the Freemasons. |
| 7 | The decoded message is a sentence. In the flag, join the words with underscores, and the sentence starts with a capital letter. |

Suggested timing: hints 1 and 2 early, hints 3 and 4 at the midpoint, hints 5 to 7 in the last stretch.

## Design notes

- **Keep the file untouched.** Do not rebuild it or re-save it in an audio editor, because that removes the attached zip.
- **Capitalization.** The decoded sentence has no capital letters, but the flag starts with a capital `I`. If your platform can accept flags case-insensitively, turn that on. Otherwise keep hint 7.
- **Pigpen glyphs.** The picture only uses letters whose shapes were checked against three public Pigpen implementations (the letters A to V).
