# pc-monstrum-doc

A measured description of one directory: 89 files, 12 subdirectories,
2,500,176,557 bytes, 87 distinct hashes, copied from the owner's GOG Galaxy
installation of **MONSTRUM** (Windows, 64-bit; `Team Junkfish` / `Monstrum` in
Unity's own `app.info`) — a Unity 5.5.0f3 build of 2018-11-20 in which
98.28 % of the bytes are nineteen serialized files and their eleven sidecars,
the code is 2.2 MB of managed metadata in the clear, and the owner's last
evening of play is a text log beside the data. Nothing here is packed,
encrypted or obfuscated; the work was to read it well, and to repair a
coverage table that called 98 % of it opaque before rendering the first
texture. The object is not published. What is published is what could be
counted, closed on, decoded, rendered and confirmed — all of it from the
object's own bytes, none of it from outside.

## The short card

| | |
| --- | --- |
| **what it is** | a first-person hide-and-run on a container ship built from prefabs by a seed (`LevelGeneration`: `roomPrefabs`, `corridorPrefabs`, `monsterSeed`, `selectedMonster`), one monster of three per run (`MonsterTypeEnum { Brute, Hunter, Fiend }`), three ways off (`ESCAPE_HELICOPTER`, `ESCAPE_LIFERAFT`, `ESCAPE_SUBMARINE`), and a diary in `output_log.txt` that logs the seed, the monster and the deck of every run |
| **title** | MONSTRUM — `Monstrum` in `app.info`, in `PlayerSettings`, in GOG's `.info`; a splash movie named `MONSTRUM SPLASH SCREEN`; the title on the menu is not a texture |
| **studio** | **Team Junkfish** — `app.info`, `PlayerSettings`, GOG's `.script` save path (`LocalLow/Team Junkfish/Monstrum`), and a picture: `JunkfishLogo_Transparent_571_475`, a mechanical angler-fish over the word `JUNKFISH` |
| **year** | 2018 by the build — 74 files at 2018-11-20 21:36–21:38; the UnityScript assemblies of 2017-05-15; the Unity player of 2016-11-24; a crash of 2018-12-15; the Galaxy install of 2025-03-20; the last play of 2026-01-31. No literal, texture or resource carries a copyright or release year |
| **engine** | Unity 5.5.0f3 (`38b4efef76f0`): nineteen SerializedFiles at format version 17, no type tree; Mono, not IL2CPP; `csc 8.00` C# and two UnityScript assemblies; plugins Steamworks.NET, InControl, Oculus (`OVRPlugin` 1.9 MB; bundle id `com.oculus.UnitySample` left in place), Tobii eye tracking, XInput; a debug console (SRDebugger) compiled in |
| **files** | 89: 19 serialized (`.assets` ×9, `level0`–`6`, `globalgamemanagers`, the engine's two), 7 `.resS`, 4 `.resource`, 28 PE (the 22 MB player, 15 managed and 11 native DLLs, the Inno stub), 20 text, 3 icons, 2 ZIPs, a BMP, a minidump, an `.mdb`, a `.lnk`, Inno's `.dat` and `.msg` |
| **bytes** | 2,500,176,557 — `.resS` 1,657,079,072 (66.28 %), `.resource` 492,065,344 (19.68 %), serialized 304,418,336 (12.18 %), PE 41,385,216 (1.66 %), everything else 5,228,589 (0.21 %) |
| **distinct sha1** | 87 — `steam_api64.dll` and `Tobii.GameIntegration.dll` shipped twice |
| **the objects** | 101,652 in 19 files, `fileSize` agreeing on 19 of 19: 28,979 GameObject, 26,577 Transform, 15,773 MonoBehaviour, 2,216 AudioClip, 1,880 MonoScript, 1,157 Mesh (133 MB, inline), 822 Texture2D, 703 Material, 388 AnimationClip, 129 Shader, 10 MovieTexture (127.5 MB), 9 Font, 3 Cubemap; seven scenes `Splash`, `Menus`, `Loading`, `MainSecondary`, `CompletionMovieScene`, `Credits`, `Collectables` |
| **the textures** | 825 (822 + 3 cubemaps) at a layout read field by field from a hex dump — version 15's plus `m_StreamData` — closing on the body end and on the byte arithmetic 825 of 825; 737 streamed into seven `.resS` that the records tile to the last byte; DXT5 606, RGBA32 82, ARGB32 42, DXT1 39, ARGB4444 33, RGB24 19, Alpha8 4; **eleven rendered and all eleven confirmed by the owner**: a fuse-box loading hint, `Brute_Diffuse_04`, `Monster02_*` (the Hunter, by the mesh `Hunter_Rig_Base:Monster02_LowRetop05`), `Monster03_*` (the Fiend, by `Fiend_Base_Rig:Monster03_Fiend_01`), `Death0`, the three escape screens (`Win` = *YOU ESCAPED* on a life raft, `Untitled` = the helicopter, `Win Screen Sub` = the submersible), the studio's logo, a crew note in pixels, the menu's paper |
| **the audio** | 2,215 FSB5 banks laid end to end in four `.resource` files, every chain ending at the last byte, matched to 2,215 `AudioClip` records with size, channels, rate and codec agreeing 2,215 of 2,215; PCM16 ×2,029, Vorbis ×186, all 44,100 Hz, 138.97 minutes; `ENV_` 980, `MOV_` 648, `ACT_` 440, `DIA_` 31, `MUS_` 24; `Hunter` in 374 names |
| **the movies** | 10 `MovieTexture` holding Ogg whole: nine `Escape_{Heli,LifeRaft,Submersible}_{Brute,Hunter,Fiend}` films, 1920 × 1080 Theora at 25 fps with no audio stream (their nine `Escape_*` clips match them to the tenth of a second: 54.6 / 42.8 / 46.0 s), and a 10-second splash with Vorbis 48 kHz; 3,181 pages, 0 bad CRCs |
| **the assembly** | `Assembly-CSharp.dll`: 29 tables, 48,025 rows — 2,124 types (1,510 the game's), 11,076 fields, 11,433 methods, 3,560 imported members, 3,015 literals — read to the member level, not the IL; `LevelGeneration` owns the seed and the monster; `MonsterEffectiveness`, `EscapeChecker`, `Helicopter`, `RaftEscapeCheck`, `Sub`, `FSM`, `MAlertMeters`, `MChasingState`; `SteamVent` is vapour |
| **the co-op** | **none in this build**: 0 of 3,015 literals, 0 of 1,510 game types, 0 of 22,509 fields and methods, 0 of 475 imported types outside Steamworks' stats/API/client, 0 of 3,560 imported members for lobby, matchmaking, networking or multiplayer; the Steamworks.NET library beside it carries 23 lobby types the game never references; `UnityEngine.Networking.dll` ships with no type imported |
| **the diary** | 2026-01-31, 17:38:50–17:50:30: four runs — seeds 1872376908 Fiend/UpperDeck, 2128268496 Hunter/LowerDeck, 1538225150 Hunter/LowerDeck, 1062072777 Brute/LowerDeck — counters 2/2/2 → 2/4/3 (a tally of runs per monster, the rule holding 9 of 9), sixteen achievement keys saved `False` ×4, no escape; the owner says they play to be chased and hide |
| **the crash** | 2018-12-15, run 57 of that install (counters 19/18/19, Hunter, UpperDeck): `crash.dmp`, 12 streams, 110 modules, 32 threads, Windows 10 build 17134 — `EXCEPTION_ACCESS_VIOLATION` reading 0x260 at `Monstrum.exe + 0x7BEC8C`, in the Unity player's `.text`, not the game's IL; dump 16:18:54 UTC = files 17:18:54 local |
| **GOG's** | manifest 74 + 1 entries, 73 present + 16 undeclared = 89, 2 absent; `.info` gameId 1950261263, buildId 51712611558478470; `.hashdb` is a ZIP; `webcache.zip` 9 members; Inno Setup 5.6.2's stub, uninstall log and messages; the GOG User Agreement; `goglog.ini` with two account names — counted, masked, not quoted |
| **crossings** | 12 of 87 over 117 repositories, all GOG's or Mono's; not a byte of the game |
| **coverage** | specified 1.8644 %, decoded 31.8571 %, derived 66.2785 %, **opaque 0 bytes**, residue 0 — from 98.2831 % opaque before the repair |
| **tools** | 609 Python files: 605 inherited (7 changed: `coverage.py`'s seven probes and sidecar pass, `cilmeta.py` to Field/MethodDef/MemberRef, `fsb5.py`'s walk and clips, `clrmeta.py`'s guard, `unityfs.py`'s format table, `inno56.py`'s refusal, `pe.py`'s usage), 4 written (`unitytex17.py`, `mdmp.py`, `unitylog.py`, `unitymovie.py`) |

**The work this time was a repair, three readers, two censuses and a
walker — on an object behind nothing.** The last objects were behind a
packer or a codec; this one was in the clear from the first byte, and what
changed is where the risk moved: not to forcing, but to *reading well* — a
layout guessed at version 17 renders a plausible wrong picture, a bank read
as a file says DISAGREES on a good file, a table that files 98 % opaque
poisons every denominator downstream. So the table was repaired first, the
texture layout was read from a hex dump and closed twice on every object
before the first pixel, and the first render went to the owner before the
audio or the assembly were touched. Out came the ship, the three monsters
(named by the object's own mesh names as the owner named them), the three
escapes as screens and as nine films, the studio's fish, a crew note that
exists only as pixels — and a co-op that is nowhere in five tables, said
with the counts in both directions and the one nuance that a library
ships everything it wraps.

**The pre-briefing's figures were corrected nine times**, two of them
about the box itself (it *did* read the metadata tables; it *did* have an
FSB5 reader). Its five hunches score one whole truth — the render — two
halves, a two-thirds and a three-eighths.

## The chapters

Eight, and the count is the content's: the serialized files had to be one
because they are the container of 98 % of the bytes and their sidecars are
tiled by their records; the textures one because they are the game's face
and the render is the proof; audio and movies one because 2,215 banks and
nine films are two censuses with the same shape; the assembly one because
the co-op question lives in its tables; the diary, the crash and GOG one
because they are the owner's evenings, and the shop's furniture around them;
and the tools one because a repair came before the first reader.

| chapter | what it says |
|---|---|
| [01 — What this is](docs/01-what-this-is.md) | the install, the split by magic, what it says in the clear, the four denominators, coverage |
| [02 — The technical sheet](docs/02-the-technical-sheet.md) | every figure with its command |
| [03 — The serialized files and their sidecars](docs/03-the-serialized-files-and-their-sidecars.md) | nineteen files, 101,652 objects, seven scenes, the ship by name, seven `.resS` and four `.resource` tiled to the last byte |
| [04 — The textures](docs/04-the-textures.md) | the version-17 layout from a hex dump, two closures on 825 of 825, eleven renders confirmed |
| [05 — The audio and the movies](docs/05-the-audio-and-the-movies.md) | 2,215 banks = 2,215 clips, PCM16 not Vorbis, nine escape films of three by three and a splash |
| [06 — The assembly and the co-op](docs/06-the-assembly-and-the-co-op.md) | the tables to Field and MethodDef, what a Monster is as types, the seed's class, the co-op at zero on five levels |
| [07 — The diary, the crash and GOG](docs/07-the-diary-the-crash-and-gog.md) | four runs with their seeds and the tally rule, the fifty-seventh run's minidump, the manifest both ways, the clocks, the owner's data counted and masked |
| [08 — Tools, predictions and calibration](docs/08-tools-predictions-and-calibration.md) | the coverage repair, seven tools changed and four written, rule 0, five hunches scored, nine corrections, the term +7.00 |

`notes/` holds the raw output of every command cited; `tools/` the box.
The object, the renders, the pre-briefing and the working directory are not
published; the notes were checked for this machine's paths and for the
object's account names before commit.
