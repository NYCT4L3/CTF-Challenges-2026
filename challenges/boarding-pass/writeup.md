# Boarding Pass: Writeup

> **Spoilers.** This file contains the full solution.

**Category:** OSINT / Forensics · **Difficulty:** Easy–Medium

## Solution

1. **Two QR codes.** The small one on the stub scans instantly and gives:

   ```
   M1HIGASHI/KAORI MS   ER4D9TZ PUSHNDBX 0823 305Y031C0112 100
   ```

   This is the decoy: Air Busan, 1 November, seat 31C, sequence 0112. The pass prints seat **12A** and sequence **0047**, so this is not her booking. Submitting it gives a confident wrong flag.

2. **The big QR will not scan.** The marker blotted out its bottom-left corner square. QR readers find a code by its three corner squares, so with one missing they fail. The two diagonal strokes through the data are not a problem, because the QR's own error correction repairs them.

3. **Redraw the corner square.** Copy the top-left corner square into the bottom-left corner, or draw it using the pixel recipe below. Clean up the leftover marker around it, or the code still will not be detected.

4. **Scan again.** The real booking:

   ```
   M1HIGASHI/KAORI MS    EK7M2QX PUSHND7C 0417 307Y012A0047 100
   ```

   Seat `012A` and sequence `0047` match the printed ones.

5. **Parse the IATA BCBP fields.** Carrier `7C`, from `PUS`, to `HND`, Julian date `307`.

6. **Look the values up.**
   - `7C` is Jeju Air.
   - `HND` is Tokyo Haneda, so the city is Tokyo.
   - Day 307 of 2026: January to September is 273 days, October adds 31, so day 304 is 31 October and day 307 is **3 November 2026**.

**Flag:** `CTF{...}` (redacted; built from the airline, city and date in step 6, in the format `CTF{airline_city_date}`)

## BCBP field breakdown (real booking)

| Chars | Field | Value |
|---|---|---|
| 0 | Format code | `M` |
| 1 | Legs | `1` |
| 2–21 | Passenger name | `HIGASHI/KAORI MS` |
| 22 | E-ticket flag | `E` |
| 23–29 | Booking reference (PNR) | `K7M2QX` |
| 30–32 | From | `PUS` |
| 33–35 | To | `HND` |
| 36–38 | Carrier | `7C` |
| 39–43 | Flight number | `0417` |
| 44–46 | Julian date | `307` |
| 47 | Cabin | `Y` |
| 48–51 | Seat | `012A` |
| 52–56 | Check-in sequence | `0047` |

## Redrawing the corner square

The scan is flat (no tilt), the code has 37 modules, and each module is exactly 8 px. The big QR's white square starts at pixel (865, 230) and is 360 × 360 px. The missing corner square starts at pixel (897, 502). In Paint, GIMP or Photopea, draw these rectangles in order:

| Order | Colour | Top-left | Size | Purpose |
|---|---|---|---|---|
| 1 | white | (889, 494) | 72 × 72 | wipes the marker and the white gap |
| 2 | black | (897, 502) | 56 × 56 | outer ring of the corner square |
| 3 | white | (905, 510) | 40 × 40 | inner white ring |
| 4 | black | (913, 518) | 24 × 24 | centre block |

Step 1 matters: without it, the leftover marker stops the code from decoding. The repair still decodes if the rectangles are up to 4 px off in any direction.

## Hints

| # | Cost | Hint |
|---|---|---|
| 1 | cheap | There are two barcodes. Only one of them belongs to the seat on the ticket. |
| 2 | cheap | A QR code is found by its three corner squares. Count them on the big one. |
| 3 | medium | Draw the missing corner square: black 7×7 modules, white 5×5 inside, black 3×3 in the centre. Each module is 8 px wide. Wipe the marker around it too. |
| 4 | medium | The barcode follows an IATA standard (BCBP). The date is a day-of-year number. |
| 5 | expensive | The missing square starts at pixel (897, 502). |

## Design notes

- The decoy is the main trap: a solver who decodes whatever scans gets a plausible but wrong answer (Air Busan, 1 November).
- The real code fails in OpenCV unless all three corner squares are present.
- An earlier version was too hard (tilted photo, 45-module code). This version uses a flat scan, a smaller 37-module code with 8 px modules, and an explicit "scribbled over the barcode" line in the description.
- The passenger, booking references and flight numbers are invented. The airline and airport codes are real; no logos are used.
- **Easter egg (not part of the solution):** the EXIF "Image Description" field reads `WHAT THE HELL IS HAPPENING IN SHIBUYA`, a Jujutsu Kaisen reference. It shows up with `exiftool boarding_pass.jpg`.

## Real-world lesson

A booking reference plus a surname can be enough to manage someone's booking, so photos of boarding passes should not be posted online, even with parts blacked out. The barcode itself still carries the booking details.
