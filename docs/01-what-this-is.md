# 01 — What this is: a 2.5 GB GOG Galaxy install of a Unity 5.5 game whose nineteen serialized files the box could read and its coverage table could not, and which carries its owner's own play diary

*Measure: `python tools/treecensus.py Monstrum` (notes/treecensus.txt), `python tools/hashall.py Monstrum` (notes/hashall.txt), `python tools/coverage.py tree --root Monstrum` (notes/coverage.txt), `python tools/unityfs.py census Monstrum/Monstrum_Data` (notes/unityfs-census.txt), `python tools/unityfs.py verify Monstrum/Monstrum_Data` (notes/unityfs-verify.txt).*

## The object

One directory, `Monstrum\`, copied whole from the owner's GOG Galaxy
installation with its modification times kept. Its location on the drive it
came from is a byte of the object (the logs and the shortcut carry it) and is
not written here:

    Monstrum\    89 files    12 directories    2,500,176,557 bytes    87 distinct SHA-1

Two of the 89 hash alike with two others: `steam_api64.dll` and
`Tobii.GameIntegration.dll` are each shipped twice, in the root and in
`Monstrum_Data\Plugins\`. Nothing was run, installed or emulated; every
figure below was read out of bytes as data, with a public format where one
exists and a closure where none does.

The owner's framing was: *Monstrum*, "a well-known recent indie", "you are on
a ship and you run — also co-op — from a monster chosen at random among three
at the start", "I see a `Mono` folder and think Unity". Five claims. The bytes
answer four of them with a witness and the fifth with a count of zero at five
levels ([06](06-the-assembly-and-the-co-op.md)): Unity is `5.5.0f3` at offset
20 of nineteen files; the three monsters are an enum `MonsterTypeEnum { Brute,
Hunter, Fiend }` in the game assembly and three skins in the textures
([04](04-the-textures.md)); the ship is 1,680 GameObjects with `Deck` in the
name and seven `level` files ([03](03-the-serialized-files-and-their-sidecars.md));
"recent" is a build of 2018-11-20 21:36–21:38 by the directory records; and
"co-op" is not among 3,015 string literals, 2,124 types, 22,509 fields and
methods, 475 imported types or 3,560 imported members. The owner also said,
while the renders came out, that they play this game to be chased and to hide
rather than to escape — and the log agrees, four runs in eleven minutes and
no `ESCAPE_*` saved `True` ([07](07-the-diary-the-crash-and-gog.md)).

## The split

Classified by magic, not by name (`coverage.py tree`, after the repair in
[08](08-tools-predictions-and-calibration.md)):

| family | files | bytes | share |
|---|---:|---:|---:|
| `.resS` — the textures' pixels, streamed out of the serialized files; no magic, named by 737 `m_StreamData` records | 7 | 1,657,079,072 | 66.2785 % |
| `.resource` — FMOD Sample Bank 5, one bank per AudioClip laid end to end (2,215 banks) | 4 | 492,065,344 | 19.6812 % |
| Unity SerializedFile, format version 17: nine `.assets`, `level0`–`level6`, `globalgamemanagers`, `unity default resources`, `unity_builtin_extra` | 19 | 304,418,336 | 12.1759 % |
| PE — `Monstrum.exe` (Unity's player, 22,161,920), `unins000.exe`, 15 managed and 11 native DLLs | 28 | 41,385,216 | 1.6553 % |
| `unins000.dat` — Inno Setup's uninstall log | 1 | 3,094,667 | 0.1238 % |
| plain text: 19 ASCII (`.ini`, `.config`, `.aspx`, `.txt`, `.info`, `.script`, `.log`, `app.info` …) and 1 UTF-8 (`goglog.ini`) | 20 | 976,182 | 0.0390 % |
| `UnityEngine.dll.mdb` — Mono debug symbols for the engine assembly | 1 | 413,678 | 0.0165 % |
| three Windows icons | 3 | 273,598 | 0.0109 % |
| two ZIPs — `webcache.zip` (9 members) and `goggame-1950261263.hashdb` (1 member) | 2 | 243,492 | 0.0097 % |
| `crash.dmp` — a Windows minidump | 1 | 155,324 | 0.0062 % |
| `ScreenSelector.bmp` | 1 | 47,670 | 0.0019 % |
| `unins000.msg` — Inno Setup's compiled messages | 1 | 23,147 | 0.0009 % |
| `Launch Monstrum.lnk` — a Shell Link | 1 | 831 | 0.0000 % |
| | **89** | **2,500,176,557** | **100 %** |

The three Unity families are 98.28 % of the bytes; everything Unity wrote is
dated 2018-11-20; GOG's, the crash's and the owner's files are the other
1.72 %. Thirteen rows sum to the object (checked by hand: 2,500,176,557):
`coverage.py`'s residue is 0.

## What the object says about itself, in the clear

* `Monstrum_Data\app.info` (22 bytes): `Team Junkfish` LF `Monstrum` — the
  company and product names Unity writes at build time; the same two strings
  are in `PlayerSettings` inside `globalgamemanagers`, and the studio's save
  path `LocalLow/Team Junkfish/Monstrum` is in GOG's `.script`. The studio is
  also a picture: `JunkfishLogo_Transparent_571_475`, a mechanical angler-fish
  over the word `JUNKFISH`, the only texture in `sharedassets5.assets.resS`
  ([04](04-the-textures.md)).
* Every serialized file, offset 20: `5.5.0f3` (eighteen) or `5.5.0b10` (the
  engine's own `unity default resources`, a beta's). Both logs: `Initialize
  engine version: 5.5.0f3 (38b4efef76f0)`. `BuildSettings` lists seven scenes
  in order: `Splash`, `Menus`, `Loading`, `MainSecondary`,
  `CompletionMovieScene`, `Credits`, `Collectables`. `PlayerSettings` carries
  the bundle identifier `com.oculus.UnitySample` — the Oculus sample project's,
  never renamed.
* `Assembly-CSharp.dll`: 2,124 types, among them `LevelGeneration` (which
  owns `monsterSeed`, `selectedMonster`, `chosenMonstType`, `allMonsters`,
  `monsterSpawnPoints`), `MonsterTypeEnum` (`Brute`, `Hunter`, `Fiend`, in
  that order), `MonsterEffectiveness`, `EscapeChecker`, `Helicopter`,
  `RaftEscapeCheck`, `Sub`, `FSM`, `MAlertMeters`, `MRoomSearch`,
  `MChasingState`; and 3,015 literals, among them `'Brute'`, `'Hunter'`,
  `'Fiend'`, the sixteen achievement keys from `ESCAPE_HELICOPTER` to
  `GLOWSTICKS`, and `Loading Achievements from Steam` beside `Loading
  Achievements from Local` — the log prints the second.
* `Monstrum_Data\output_log.txt` (2026-01-31): one engine start, four runs,
  each `Test Seed: <n>` → `Brute k / Hunter k / Fiend k` → `Inc: <monster>` →
  sixteen `KEY:False` → `Inc: <deck>`. Seeds 1872376908 (Fiend, UpperDeck),
  2128268496 (Hunter, LowerDeck), 1538225150 (Hunter, LowerDeck), 1062072777
  (Brute, LowerDeck); the counters 2/2/2 → 2/2/3 → 2/3/3 → 2/4/3, the chosen
  monster's going up by one each time. The crash folder's log of 2018-12-15
  has one more run: seed 321071888, Hunter, UpperDeck, counters **19/18/19** —
  fifty-six runs by that evening, three weeks after the build.
* The pictures: `Win` paints `YOU ESCAPED` over a red life raft; `Untitled`
  is the helicopter lifting off; `Win Screen Sub` the submersible under water;
  `Death0` a corridor with a body on the floor; `Story_Note_ChefToQM` a
  hand-written note from *Mills* to *Stuart* about a rat problem and *Santos*
  and *Ocampo* — four crew names that are pixels and no string. All eleven
  renders were confirmed by the owner at sight ([04](04-the-textures.md)).

## The denominators

Four, and every figure in these documents names one:

* **89 files, 2,500,176,557 bytes, 87 distinct hashes** — the install;
* **19 serialized files, 304,418,336 bytes, 101,652 objects** — the
  container; `fileSize` agrees on 19 of 19, every object lies inside its file;
* **11 sidecars, 2,149,144,416 bytes (85.96 %)** — 7 `.resS` tiled to the
  last byte by 737 texture records, 4 `.resource` tiled to the last byte by
  2,215 banks;
* **4 + 1 runs** in the two logs, each with a seed and a monster; 3,015
  literals; 2,124 types.

## Coverage

    python tools/coverage.py tree --root Monstrum

| bucket | files | bytes | share |
|---|---:|---:|---:|
| specified — PE ×28, text ×20, icons ×3, ZIP ×2, BMP, minidump, Mono `.mdb`, Shell Link, Inno's `.dat` and `.msg` | 59 | 46,613,805 | 1.8644 % |
| decoded — Unity SerializedFile ×19, FSB5 ×4 (formats never published by Unity or FMOD; read by named third parties and by this box) | 23 | 796,483,680 | 31.8571 % |
| derived — the 7 `.resS` sidecars, named by the stream records of their sibling serialized files with every record inside the file | 7 | 1,657,079,072 | 66.2785 % |
| opaque | 0 | 0 | 0.0000 % |
| residue | | 0 | |

Before this session the same table said 54 files specified, 1.7169 %, and
**35 files opaque, 98.2831 %**: it had no probe for a SerializedFile, an FSB5
bank, a minidump, an `.mdb`, a `.lnk`, Inno's leftovers, or a sidecar with no
magic — while `unityfs.py`, which reads the first of them, sat in the same
folder. Seven probes and one tree-level pass, each with a closure and each
with positive and negative controls in the selftest (187 checks → 220), were
the first thing done ([08](08-tools-predictions-and-calibration.md)). The
*derived* bucket is this session's warrant only, and says so; *decoded* is a
third party's, and names it.

## What is not measured

* the meshes (1,157 objects, 133 MB, all inline), the animations (388 clips),
  the shaders (129), the physics, the level-generation algorithm — none owed,
  none read beyond the census ([03](03-the-serialized-files-and-their-sidecars.md));
* the IL: the metadata tables were read to the member level, the method
  bodies were not ([06](06-the-assembly-and-the-co-op.md));
* the FSB5 samples: the banks were censused, no sample was decoded to sound
  ([05](05-the-audio-and-the-movies.md));
* the movies' frames: the Ogg pages were walked and checksummed, no Theora
  frame was decoded;
* the minidump's thread contexts and memory ranges; the `.mdb`'s method
  tables; `unins000.dat`'s records — each identified by its head and left
  ([07](07-the-diary-the-crash-and-gog.md));
* the release history of the game outside this install, which the object does
  not carry and which was not consulted.
