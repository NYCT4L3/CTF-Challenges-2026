# The One-Eyed Prince: Writeup

> **Spoilers.** This file contains the full solution.

**Category:** OSINT (metadata plus research) · **Difficulty:** Medium  
**Tools:** `exiftool` or any online EXIF viewer (CyberChef's *Extract EXIF* works), a search engine, and a map.

The flag is a name written in leetspeak, so it cannot be answered from memory alone. The picture and metadata lead to the story and the character, and a number table in the metadata decides exactly how the name is written.

## The file

`one_eyed_prince.jpg` is a 2576 × 1438 JPEG, an AI-generated image of a Tokyo alley café with a sign reading `:re`, a paper lantern showing `993`, one red spider lily and one white one.

## Solution

### 1. Look at the picture

A narrow Tokyo-style alley at dusk, a café whose sign reads `:re`, a paper lantern with `993` written on it (stacked vertically as 9, 9, 3), a red spider lily in the foreground and a white one on the right.

### 2. Read the metadata

```
exiftool one_eyed_prince.jpg
```

CyberChef's *Extract EXIF* operation shows the same fields (it reports 11 tags). The fields that matter:

```
Image Description : White turns red when it meets blood.
Artist            : A=4, B=22, C=47, D=71, E=3, F=72, G=89, H=20, I=1, J=35,
                    K=83, L=86, M=78, N=60, O=80, P=77, Q=69, R=82, S=63, T=37,
                    U=2, V=17, W=21, X=43, Y=19, Z=64
GPS Position      : 35 deg 40' 34.32" N, 139 deg 39' 1.08" E
```

### 3. Decode the clues

- **Image Description:** a white flower turning red when it meets blood. Together with the red and white spider lilies in the picture, it is a story reference.
- **GPS:** the coordinates are in Tokyo.
- **Lantern and description:** the challenge text asks for 1000 minus 7, and the lantern shows 993, the first answer. This is the famous countdown by sevens from the story. The `993` is a decoy for the table: it looks like it belongs to the A to Z numbers, but it belongs to the story.
- **The table:** it looks like a cipher key, but only some entries matter (see step 5).

### 4. Identify the story and the character

Search for the clues together, for example `1000 minus 7 Tokyo red spider lily`. The results lead to *Tokyo Ghoul*, and the `:re` sign is a nod to the sequel, *Tokyo Ghoul:re*. The main character is Kaneki, and his friends call him Kaneki-kun.

### 5. Apply the table the right way

The table has 26 numbers, but 23 of them are random decoys. Only three look like leetspeak: `A=4`, `E=3` and `I=1`. The hint ladder tells players that the flag contains three numbers and that every other character is a letter. So only the letters **a**, **e** and **i** are replaced by their table numbers:

- `kaneki` contains one `a`, one `e` and one `i`, so all three numbers are used there.
- `kun` has no `a`, `e` or `i`, so it stays as letters.

A player who swaps *every* letter with its table number gets a long string of digits. That is the trap.

### 6. Build the flag

Join the two parts with an underscore, all lowercase: `CTF{name_suffix}`.

**Flag:** `CTF{...}` (redacted; built from the name and suffix above)

## Hints

| # | Hint |
|---|---|
| 1 | Photos can carry more than pixels. |
| 2 | One clue is on the map. |
| 3 | The sign is the name of a café. Which story does it belong to? |
| 4 | A red flower, Tokyo, and a countdown by sevens. The story is Tokyo Ghoul. Now find the answer. |
| 5 | The answer is a name. The table in the metadata is mostly noise. |
| 6 | The flag has three numbers. Every other character is a letter. |
| 7 | Flag format: `CTF{name_suffix}`, all lowercase. |

Suggested timing: hints 1 and 2 early, hint 3 at the midpoint, hints 4 to 7 in the last stretch.

## Design notes

- **Why version 2 exists:** the first version's flag was just the character's name, so recalling it from memory was enough. Version 2 adds the leetspeak rule and a full A to Z number table, almost all of it random decoys. The exact flag text exists only in the table-plus-rule combination.
- The metadata holds only the three clues above, and the decoy values contain no `1`, `3` or `4` and nothing else that looks like leetspeak.
- The table is stored in the Artist field because CyberChef's *Extract EXIF* does not display the UserComment field.
- **Keep the file untouched.** Resizing, converting or re-saving the image removes the metadata, and the challenge becomes unsolvable.
- **Fan reference.** The image is AI-generated and refers to a real anime. Consider whether you want to republish it outside the event.
