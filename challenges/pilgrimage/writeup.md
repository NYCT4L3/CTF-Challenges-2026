# Pilgrimage: Writeup

> **Spoilers.** This file contains the full solution.

**Category:** OSINT · **Difficulty:** Easy

## Solution

1. **Identify the real place.** The photo shows the Santa Monica Pier in California: the Ferris wheel, the amusement park and the entrance arch. A reverse image search finds it, and the words `PACIFIC PARK` are readable on the hub of the Ferris wheel.
2. **Find the game link.** The clue about a game with missions, plus a search for the pier together with GTA, shows that GTA V's Del Perro Pier is closely modelled on it. Rockstar's version of Los Angeles is Los Santos.
3. **Find the mission.** The GTA wiki pages for Del Perro Pier and the GTA Online contact missions list *Pier Pressure*: a GTA Online mission given by Gerald, in which you go to a meet under Del Perro Pier and take out the gang members involved.
4. **Build the flag** from the contact and the mission name, in the format `CTF{contact_mission}`.

**Flag:** `CTF{...}` (redacted; built from the contact and mission found in step 3)

## Hints

| # | Cost | Hint |
|---|---|---|
| 1 | cheap | The photo shows a real pier in California. |
| 2 | cheap | The game is GTA V (and GTA Online). Its Los Angeles is called Los Santos. |
| 3 | medium | The pier is called Del Perro Pier in the game. Look for missions that happen there. |
| 4 | medium | The mission is a GTA Online contact mission. Its name contains the word "pier". |

## Design notes

- The photo has no EXIF, GPS, XMP or IPTC data, and nothing hidden after the end of the JPEG, so nothing needed stripping.
- The legible `PACIFIC PARK` sign on the Ferris wheel makes step 1 very easy, which fits the easy difficulty. Cropping or blurring the hub would make it harder.
- The mission payout is deliberately not part of the flag, because GTA Online payouts have changed over the years.
- Other missions also visit the pier. The phrase "a street contact once sent me on a job under it" is what narrows it down to a single mission.
