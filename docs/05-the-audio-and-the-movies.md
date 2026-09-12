# 05 — The audio and the movies: 2,215 FSB5 banks that are 2,215 clips, nine escape films of three by three, and one splash

*Measure: `python tools/fsb5.py walk Monstrum/Monstrum_Data/sharedassets{0,1,3,4}.resource` and `python tools/fsb5.py clips Monstrum/Monstrum_Data --list` (notes/fsb5-audio.txt), `python tools/unitymovie.py list|census Monstrum/Monstrum_Data` (notes/unitymovie-census.txt), `python tools/unityfs.py paths Monstrum/Monstrum_Data/globalgamemanagers`, the two selftests (notes/selftests.txt).*

## The banks

A Unity 5.5 `AudioClip` keeps a header in the serialized file and its
samples in a `.resource` sidecar, as an **FMOD Sample Bank 5**: `FSB5`, a
version (1 here), a sample count, the sizes of the sample-header block, the
name table and the data, a codec word, then one bit-packed 64-bit word per
sample (rate index, channels, offset, length in samples). The format is
FMOD's and unpublished; the public accounts are third parties' (python-fsb5,
vgmstream); `fsb5.py`, written on `android-talesofluminaria-doc`, reads it
from them — and reads *one bank per file*, which on this object is wrong:
here a `.resource` is **one bank per clip, laid end to end**, 2,197 of them in
`sharedassets3.resource`, and the pre-briefing's `info` on it reported the
first bank's 262,944 bytes against a 454,526,592-byte file and said
DISAGREES. `walk`, added here, steps from bank to bank by each one's declared
length and closes on the file:

| `.resource` | bytes | banks | codecs | channels | seconds | chain |
|---|---:|---:|---|---|---:|---|
| `sharedassets0` | 529,344 | 1 | PCM16 | stereo | 3.00 | ends at the last byte |
| `sharedassets1` | 15,285,504 | 8 | PCM16 ×6, Vorbis ×2 | mono 6, stereo 2 | 329.01 | ends at the last byte |
| `sharedassets3` | 454,526,592 | 2,197 | PCM16 ×2,022, Vorbis ×175 | mono 1,958, stereo 239 | 7,575.97 | ends at the last byte |
| `sharedassets4` | 21,723,904 | 9 | Vorbis ×9 | stereo 9 | 430.26 | ends at the last byte |
| | **492,065,344** | **2,215** | PCM16 2,029, Vorbis 186 | mono 1,964, stereo 251 | **8,338.24** (138.97 min) | 4 of 4 |

Every bank is at 44,100 Hz; none carries a name table (the name is the
clip's). The pre-briefing's hunch that the samples would be Vorbis is wrong
for 2,029 of 2,215: this build ships its short sounds as 16-bit PCM and
compresses only the long ones — the 24 `MUS_` loops, the `ATMO_` and `AMBI_`
beds, the nine `Escape_` tracks.

## The clips

`fsb5.py clips` reads the 2,216 `AudioClip` bodies of the nineteen serialized
files at the version-17 layout (name; load type, channels, frequency, bits,
length; a tracker flag; a subsound index; three bools; `m_Resource` = path,
u64 offset, u64 size; a compression format) and matches each record to the
bank at its offset:

    AudioClip objects        2216  (0 bodies did not parse)
    with a resource record   2215   without 1
    matched to a bank        2215
      size == bank length    2215
      channels agree         2215
      frequency agrees       2215
      codec agrees           2215

Four agreements on 2,215 of 2,215, each bank claimed by exactly one clip,
and the one clip without a record is `MONSTRUM SPLASH SCREEN audio` in
`sharedassets0.assets`: 0 channels, 0 Hz, 0 seconds — an empty clip beside
the splash movie, whose sound is inside the movie's Ogg (below). The clip's
`m_CompressionFormat` (0 = PCM, 1 = Vorbis) agrees with the bank's codec
word on every one, which is the cross-check between Unity's record and
FMOD's.

The names, by prefix over 2,216:

    ENV_ 980   MOV_ 648   ACT_ 440   ANI_ 35   DIA_ 31   MUS_ 24   UI_ 23
    AMBI_ 17   Escape_ 9   ATMO_ 6   (and NOTHING, MONSTRUM SPLASH SCREEN audio, JF Logo)

`Hunter` is in 374 clip names, `Brute` in 91, `Fiend` in 66, `Monster` in
84; `DIA_` is dialogue, 31 lines. The longest clips are the music loops:
`MUS_HunterWanderLoop` 263.01 s, `MUS_BruteWanderLoop` 256.11 s,
`MUS_RadioFunkMono_00`–`03` (254.64, 240.83, 218.86, 193.39 s — the radio the
ship plays), `MUS_WanderNoSpotLoop` 235.10 s, `ATMO_WaveLoopNew` 208.88 s,
`ATMO_WindLoopNew` 202.00 s, `MUS_HunterHideLoop` and `MUS_HunterChaseLoop`
158.92 s each. The assembly's 144 `Noises/…` and 10 `Music/…` literals
(`Noises/Hunter/Breathing/Long/In`, `Noises/Fiend/Using Power/SubDoorPull`,
`Music/NoAlert/Fiend`) are keys into the game's own audio libraries — the
`audioLibraries` field of `LevelGeneration` — and not paths of the
`ResourceManager`, whose 34 entries hold no sound at all
([03](03-the-serialized-files-and-their-sidecars.md)).

## The movies

Unity 5's `MovieTexture` keeps its film **inside the object**: after the
name, a loop flag, a PPtr to an `AudioClip`, a length and that many bytes of
Ogg, then a colour-space word. `unitymovie.py` slices the Ogg out, walks it
with `oggcensus.py`'s page reader (RFC 3533 page lengths, the Ogg CRC on
every page) and reads the two identification headers — Theora's (Xiph's
specification §6.2: picture size, frame rate, keyframe granule shift) and
Vorbis I's:

| movie | file | bytes | pages | video | length |
|---|---|---:|---:|---|---:|
| `Escape_Heli_Fiend` | `resources.assets` | 9,515,452 | 228 | 1920 × 1080, 25 fps | 54.6 s |
| `Escape_Heli_Brute` | | 13,657,289 | 338 | | 54.6 s |
| `Escape_Heli_Hunter` | | 12,468,818 | 273 | | 54.6 s |
| `Escape_LifeRaft_Fiend` | | 10,370,749 | 264 | | 42.8 s |
| `Escape_LifeRaft_Hunter` | | 17,777,935 | 373 | | 42.8 s |
| `Escape_LifeRaft_Brute` | | 14,844,277 | 314 | | 42.8 s |
| `Escape_Submersible_Fiend` | | 16,163,429 | 363 | | 46.0 s |
| `Escape_Submersible_Hunter` | | 15,666,343 | 340 | | 46.0 s |
| `Escape_Submersible_Brute` | | 15,593,041 | 339 | | 46.0 s |
| `MONSTRUM SPLASH SCREEN` | `sharedassets0.assets` | 1,491,207 | 349 | 1920 × 1080, 29.86 fps, + Vorbis 48 kHz stereo | 10.0 s |
| | | **127,548,540** | **3,181** | 0 bad CRCs; 10 of 10 tile their bytes and close their body | 440.4 s |

Three escapes × three monsters: a film for every ending, and its length is
the escape's, not the monster's (54.6 s by helicopter, 42.8 s by raft, 46.0 s
by submersible). **The nine escape films carry no audio stream**: their sound
is the nine `Escape_{Heli,LifeRaft,Submersible}_{Brute,Fiend,Hunter}`
AudioClips of `sharedassets3.resource`, Vorbis, stereo, whose lengths match
the films to the tenth of a second (54.64, 42.66–42.80, 46.04 s). The
`ResourceManager` reaches the nine as `video/escape_*` (path IDs 31–39), the
only movies it names; the splash, with its own Vorbis track inside the Ogg,
is in the splash scene's shared file. The pre-briefing's "ten movies, all in
`resources.assets`, 2,696 `OggS` pages" is nine there and one elsewhere, and
2,832 + 349 pages ([08](08-tools-predictions-and-calibration.md)).

## What is not measured

* no sample was decoded to sound and no frame to a picture: the banks were
  censused to their headers and the films to their pages and identification
  headers, which is what was owed;
* the 2,215 clips' contents — what `DIA_` says, what the 31 dialogue lines
  are — is audio, not read;
* the `Noises/…` and `Music/…` keys against the clip names: 154 literals, 2,216 names,
  matched by nobody here;
* the Theora granule arithmetic was checked against the Vorbis one only on
  the splash (10.0 s both ways) and on the selftest's hand-built stream; the
  nine escape films have no second stream to agree with.
