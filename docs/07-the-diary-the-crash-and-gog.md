# 07 — The diary, the crash and GOG: four runs of one evening in 2026 with their seeds and monsters, a fifty-seventh run of 2018 that ended in an access violation inside the player, and the shop's furniture around them

*Measure: `python tools/unitylog.py summary|runs|paths FILE` on both logs (notes/unitylog.txt), `python tools/mdmp.py info|streams|modules Monstrum/2018-12-15_165138/crash.dmp` (notes/mdmp-crash.txt), `python tools/gogmanifest.py --root Monstrum` (notes/gogmanifest.txt), `python tools/zipdir.py …` (notes/zipdir.txt), `python tools/inno56.py ldr Monstrum/unins000.exe` (notes/inno-refuse.txt), `python tools/pecensus.py Monstrum` (notes/pecensus.txt), `find Monstrum -type f -printf …` (notes/mtimes.txt), `python tools/sift.py Monstrum --group personal` (notes/sift.txt), `python tools/crossall.py …` (notes/crossall.txt).*

## The diary

A Unity 5 player writes `output_log.txt` beside its data and the game's
`Debug.Log` lines go into it. MONSTRUM prints, in this order and with these
prefixes — read off both logs, and the literals `Test Seed: `, `Count data:`,
`Inc: `, `Saved: ` are in the assembly's heap at rows 886, 180, 177 and 689
([06](06-the-assembly-and-the-co-op.md)):

    Test Seed: <n>           Level Test: <n>
    Count data:   Brute k   Hunter k   Fiend k      Inc: <the monster chosen>
    Achievements setup    Loading Achievements from Local    Saved to local
    Saved: ESCAPE_HELICOPTER:False   ... fifteen more KEY:False
    <date> <time>-------   (a wall clock)
    Count data:   LowerDeck k   UpperDeck k          Inc: <the start deck>

`Monstrum_Data\output_log.txt` — 292,112 bytes, 4,362 lines, dated
2026-01-31 17:56:25 — is one engine start (`5.5.0f3 (38b4efef76f0)`, Direct3D
11, an RTX 3060 Ti) and **four runs**:

| run | seed | counters before | monster | deck | keys saved | `True` | clock |
|---:|---:|---|---|---|---:|---:|---|
| 1 | 1872376908 | 2 / 2 / 2 | Fiend | UpperDeck | 16 | 0 | 17:39:24 → 17:43:09 |
| 2 | 2128268496 | 2 / 2 / 3 | Hunter | LowerDeck | 16 | 0 | 17:43:10 → 17:47:10 |
| 3 | 1538225150 | 2 / 3 / 3 | Hunter | LowerDeck | 16 | 0 | 17:47:12 → 17:50:21 |
| 4 | 1062072777 | 2 / 4 / 3 | Brute | LowerDeck | 16 | 0 | 17:50:23 → (file closed 17:56:25) |

The three counters are a **tally of runs per monster**: the one named in
`Inc:` is one higher at the next `Count data:`, and `unitylog.py runs` checks
this on every step — 9 of 9 between consecutive runs. The deck counters do
the same (`LowerDeck 3 / UpperDeck 3`, then `Inc: UpperDeck`). The sixteen
achievement keys, from `ESCAPE_HELICOPTER` to `GLOWSTICKS`, are saved `False`
four times; no `ESCAPE_*` is ever `True`. Sixteen clock stamps run from
17:38:50 to 17:50:30: **four runs in eleven minutes and forty seconds**,
three of them under four minutes, the last one six. The owner, told this
while the textures were being rendered, said what the log had already said:
they play to be chased and to hide, not to escape.

The monster "chosen at random among three" is chosen four times here — Fiend,
Hunter, Hunter, Brute — each with its seed, by `LevelGeneration`, which owns
`monsterSeed` and `selectedMonster` ([06](06-the-assembly-and-the-co-op.md)).
Whether the seed *is* the choice, and what the tally weights, is a method's
body and is not read; that the two are printed side by side, every run, is
the object's.

## The crash

`2018-12-15_165138\` holds three files dated 2018-12-15 17:18:54 — three
weeks and three days after the build. Its `output_log.txt` (97,291 B, 1,278
lines) is the same shape: one engine start (a GTX 970), **one run**, seed
321071888, **counters 19 / 18 / 19**, Hunter, UpperDeck, sixteen keys `False`,
clock 17:07:42 → 17:08:15, then `Crash!!!` and `**** Crash! ****`. Fifty-six
runs had been played on that install by that evening and none escaped, on
the same drive letter and folder the 2026 log names; the 2025 Galaxy install
reset the tally to 2 / 2 / 2 and kept the crash folder. It is the owner's
crash.

`error.log` (24,970 B) is Unity's text report: `Monstrum.exe caused an Access
Violation (0xc0000005) in module Monstrum.exe at 0033:11d5ec8c`, `Read from
location 00000260`, a register dump, a stack, seven modules with 32-bit
bases, `Error occurred at 2018-12-15_171856`, and the account that ran it
(counted, not quoted). `crash.dmp` (155,324 B) is a Windows minidump, whose
layout Microsoft publishes (`minidumpapiset.h`), read by `mdmp.py`:

    version word    0xA0EEA793   (MINIDUMP_VERSION 0xA793)
    time stamp      1544890734 = 2018-12-15 16:18:54 UTC
    streams         12: ThreadList (1,540 B), ModuleList (11,884), MemoryList (612),
                    Exception (168), SystemInfo (56), MiscInfo (1,364),
                    SystemMemoryInfo (492), ProcessVmCounters (152), 4 unused
    system          x64, 8 processors, Windows 10.0 build 17134
    threads         32     memory ranges  38, 85,504 bytes captured
    modules         110, of which 7 from the install: Monstrum.exe, mono.dll,
                    OVRPlugin.dll, XInputInterface64.dll, CSteamworks.dll,
                    steam_api64.dll, Tobii.GameIntegration.dll
    exception       0xC0000005 EXCEPTION_ACCESS_VIOLATION, thread 5932,
                    read at 0x0000000000000260,
                    address 0x00007FF711D5EC8C = Monstrum.exe + 0x7BEC8C
                    (base 0x00007FF7115A0000, size 23,150,592)

The fault is **in the Unity player's own code** — `.text` of `Monstrum.exe`,
0x7BEC8C into a 17.2 MB section — reading offset 0x260 of a null-ish
pointer; not in `mono.dll`, not in the game's IL. The player is Unity's file
version 5.5.0.46319. Three clocks agree once the zone is read: the dump's
16:18:54 UTC, the report's 17:18:56 and the files' 17:18:54 local are one
moment at UTC+1; the folder's name, `165138`, is 27 minutes earlier — the
process's start, by the log's first stamp at 17:07:42 being inside it. And a
measurement on the side: of the 110 modules' COFF stamps only 23 fall in
2005–2018; the other 87 read 1970, 1972, 2037, 2066, 2068, 2103 … — Windows
10's system DLLs carry a hash in the stamp field, not a date — while the 7 of
the install carry real ones (Monstrum.exe 2016-11-24, mono.dll 2016-10-26).

## GOG's

Eleven files are the shop's, none of them the game's, and `gogmanifest.py`
closes the manifest both ways:

| | |
|---|---|
| `goggame-galaxyFileList.ini` (3,245 B) | 74 + 1 entries; **73 declared and present** (2,494,589,005 B), **2 declared and absent** (`43c1cdac9fc3560f440dfaa7bfab49dd`, `__redist\ISI\scriptinterpreter.exe`), **16 present and not declared** (5,587,552 B: the uninstaller's four files, the two logs, the crash folder's three, three icons, the EULA, `webcache.zip`, the shortcut, `goglog.ini`, the manifest itself); 73 + 16 = 89 and the bytes add up |
| `goggame-1950261263.info` (790 B) | JSON: `gameId` 1950261263, `buildId` 51712611558478470, `clientId` 51549987695331069, `osBitness ["64"]`, `language neutral`, one `FileTask` → `Monstrum.exe`, one `URLTask` → `www.gog.com/support/monstrum` |
| `goggame-1950261263.script` (421 B) | one action, `savePath` = `{userappdata}/../LocalLow/Team Junkfish/Monstrum` — the studio's name a third time |
| `goggame-1950261263.hashdb` (2,571 B) | a ZIP (`PK\3\4`) of one deflated member named like itself, 73,932 → 2,423 bytes; not opened further |
| `webcache.zip` (240,921 B) | 9 deflated members, 249,106 B: 3 JPEG, 5 PNG, 1 JSON — the store page's cache, dated 2018-11-02 |
| `EULA.txt` (38,181 B) | the GOG.com User Agreement (`GOG sp. z o.o., ul. Jagiellonska 74, 03-301 Warsaw`), not the game's |
| `gog.ico`, `support.ico` (2017-08-09), `goggame-1950261263.ico` (2018-11-02) | Windows icons |
| `Launch Monstrum.lnk` (831 B) | a Shell Link ([MS-SHLLINK]), 2025-03-20; its target carries the install's drive and folder, which is why it is not quoted |
| `goglog.ini` (176 B, UTF-8) | two save-folder paths under **two Windows account names** — counted (`sift.py`: home-directory shape ×2), masked, not published |
| `unins000.exe` (1,343,048 B) | Inno Setup 5.6.2's uninstaller stub, COFF 2018-08-20, PE32; the same hash as five other GOG installs in the collection; `inno56.py` refuses it in one line (no SetupLdr table: an uninstaller has none) |
| `unins000.dat` (3,094,667 B), `.msg` (23,147 B), `.ini` (41 B) | `Inno Setup Uninstall Log (b)`, `Inno Setup Messages (5.6.2) (u)`, `productID=1950261263`; filed by their 64-byte IDs, records not read |

Twelve of the 87 hashes cross the 117 other repositories of the collection
and every one is GOG's or Mono's: the stub, `unins000.msg`, `gog.ico`,
`support.ico`, and eight of Mono's stock `etc\mono\` files (`browscap.ini`,
two `DefaultWsdlHelpGenerator.aspx`, `config`, `web.config`, `settings.map`,
`Compat.browser`, `config.xml`), the same as in `pc-iamsetsuna-doc` and
`pc-themurderofsonicthehedgehog-doc`. Not a byte of the game crosses.

## The clocks

| when | files | what |
|---|---:|---|
| 2015-03-22 … 2017-05-04 | — | COFF stamps of the native plugins (XInput, Steam, CSteamworks, Oculus, InControl, Tobii) |
| 2016-10-26, 2016-11-24 | — | COFF stamps of Mono's assemblies and of the Unity player and engine |
| 2017-05-15 | — | COFF stamps of the two UnityScript assemblies (the C# ones are zeroed) |
| 2017-08-09 | 2 | `gog.ico`, `support.ico` |
| 2018-08-20 | — | COFF stamp of the Inno stub |
| 2018-11-02 | 2 | `goggame-1950261263.ico`, `webcache.zip` |
| **2018-11-20 21:36–21:38** | **74** | the build: 23 files at 21:36 (the player, the root DLLs), 50 at 21:37 (the assemblies, the serialized files, the sidecars), 1 at 21:38 |
| 2018-12-15 17:18:54 | 3 | the crash folder |
| 2025-03-20 00:28:49–50 | 7 | the Galaxy install: `unins000.*`, `goglog.ini`, `Launch Monstrum.lnk`, the manifest |
| 2026-01-31 17:56:25 | 1 | the last play session's log |

Four clocks with the studio's hand on them: the UnityScript stamps of May
2017, the build of 20 November 2018, the crash of 15 December 2018, the play
of 31 January 2026. No literal, no texture, no resource carries a copyright
year or a release date; "2018" is the build's, and the gamelist cell says so.

## What the object carries of its owner, and what is done with it

Counted with `sift.py` and by the tools that read the files, never copied:
two account names in `goglog.ini`; one in `error.log` (`run by`); one
home-directory path in `crash.dmp` (outside the module list — none of the
110 modules is under a user profile); the install's drive and folder in both
logs (21 and 217 path occurrences) and in the shortcut; and, in two
plugins, the PDB paths of their authors' machines (`CSteamworks.dll`,
`InControlNative.dll`, one each). `mdmp.py` and `unitylog.py` mask every
directory they print; `sift.py`'s `--show` output stays in `_work\`;
`redactnotes.py --check` finds nothing in `notes\`.

## What is not measured

* the meaning of the seed — whether `Test Seed` alone decides the monster —
  and the weights the tally feeds, both in method bodies
  ([06](06-the-assembly-and-the-co-op.md));
* the crash's cause beyond its address: the thread contexts and the 38
  memory ranges were not read, and the player has no symbols here
  (`UnityEngine.dll.mdb` is the managed engine's, not `Monstrum.exe`'s);
* `unins000.dat`'s records against the 89 files, the `.hashdb` member's
  content, the `webcache.zip` pictures;
* the 2018 log's 1,219 "other" lines and the 2026 log's 4,203 (stack traces
  of the `Debug.Log` calls, in the main).
