# CTF Challenges

Challenges I designed and built for a CTF. Each challenge has its own folder with the player-facing description, the attachments players received, and a writeup.

## Challenges

| Challenge | Category | Difficulty | Attachment |
|---|---|---|---|
| [Pilgrimage](challenges/pilgrimage/) | OSINT | Easy | `pier.jpg` |
| [Boarding Pass](challenges/boarding-pass/) | OSINT / Forensics | Easy–Medium | `boarding_pass.jpg` |
| [The One-Eyed Prince](challenges/one-eyed-prince/) | OSINT (metadata) | Medium | `one_eyed_prince.jpg` |
| [Dead Air](challenges/dead-air/) | Forensics | Medium | `silence.wav` |
| [IYKYK](challenges/iykyk/) | Crypto / Misc | Medium–Hard | `challenge.txt` |

## Layout

```
challenges/
  <challenge-name>/
    README.md      player-facing description, flag format, attachments
    files/         exactly what players were given
    writeup.md     intended solution and hints (spoilers)
```

## Spoilers

Every `writeup.md` contains the full solution. If you want to play the challenges yourself, stick to each challenge's `README.md` and `files/` folder.

Flags in the writeups are redacted as `CTF{...}`; the answer is built from the values shown in the solution steps.

## License and reuse

Copyright (c) 2026 Shadow. Licensed under [CC BY-NC-ND 4.0](LICENSE).

You are welcome to solve, study and share these challenges and writeups, with credit and a link back. You may not use them commercially, and you may not share modified versions.

Want to use a challenge at your own CTF or course? Please get in touch first through my GitHub profile or by opening an issue.

Images and other third-party material in the attachments are not covered by this license and remain under their own terms.
