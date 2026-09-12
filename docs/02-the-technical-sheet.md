# 02 — The technical sheet: every figure with the command that re-derives it

*Measure: each row names its command; the outputs are in `notes/`. Paths are relative to the repository root; `Monstrum\` is the object and is not published.*

## The install

| figure | value | command |
|---|---|---|
| files, directories, bytes | 89, 12, 2,500,176,557 | `python tools/treecensus.py Monstrum` |
| distinct SHA-1 | 87 (`steam_api64.dll`, `Tobii.GameIntegration.dll` each twice) | `python tools/hashall.py Monstrum` |
| years of the directory records | 2017 ×2, 2018 ×79 (74 on 11-20, 2 on 11-02, 3 on 12-15), 2025 ×7, 2026 ×1 | `find Monstrum -type f -printf '%TY-%Tm-%Td %TH:%TM:%TS  %s  %p\n' \| sort` (notes/mtimes.txt) |
| the build's minutes | 2018-11-20 21:36 ×23, 21:37 ×50, 21:38 ×1 | same |
| coverage | specified 59 files 1.8644 %, decoded 23 files 31.8571 %, derived 7 files 66.2785 %, opaque 0, residue 0 | `python tools/coverage.py tree --root Monstrum` |
| coverage before the repair | specified 54 files 1.7169 %, opaque 35 files 98.2831 % | the same command on the inherited `coverage.py` (`_work/coverage.txt`, not published) |
| copy-protection markers | 0 of 11 schemes over 28 PE files; positive control fires in 28 of 28 | `python tools/protscan.py Monstrum` |
| crossings with the collection | 12 of 87 hashes over 117 repositories — GOG's stub, `.msg`, two icons; eight Mono config files | `python tools/crossall.py _work/sha1-all.txt --collection .. --skip pc-monstrum-doc` |
| personal identifiers | home-directory shape: 16 hits in 5 blobs (`goglog.ini` ×2, `crash.dmp` ×1, `Monstrum.exe` ×11, two plugins ×1 each); e-mail shape 264 in 13 (GOG's, OpenSSL's, Mono's, and random bytes of `.resS`) | `python tools/sift.py Monstrum --group personal` (counts; the `--show` lines stay in `_work`) |

## The serialized files

| figure | value | command |
|---|---|---|
| files, objects | 19, 101,652; format version 17 ×19; engine `5.5.0f3` ×18, `5.5.0b10` ×1 | `python tools/unityfs.py census Monstrum/Monstrum_Data` |
| `fileSize` and bounds | 19 of 19 agree; 101,652 of 101,652 objects inside their file | `python tools/unityfs.py verify Monstrum/Monstrum_Data` |
| bytes | 304,418,336 in files; 302,012,975 in object bodies | `coverage.py tree`; `unityfs.py census` |
| the classes | GameObject 28,979; Transform 26,577; MonoBehaviour 15,773; BoxCollider 5,405; MeshFilter 4,934; MeshRenderer 4,789; AudioClip 2,216; MonoScript 1,880; Mesh 1,157 (133,131,488 B); Texture2D 822; Material 703; Animator 717; AnimationClip 388 (23,842,800 B); Shader 129; MovieTexture 10 (127,549,072 B); Font 9; Cubemap 3 | `unityfs.py census` |
| the scenes | `level0` Splash, `level1` Menus, `level2` Loading, `level3` MainSecondary, `level4` CompletionMovieScene, `level5` Credits, `level6` Collectables | `python tools/strdump.py Monstrum/Monstrum_Data/globalgamemanagers` (`Assets/Scenes/*.unity`, at 0xE310) |
| GameObjects per file | sharedassets2 15,140; sharedassets3 7,835; level3 2,288; level2 1,825; level1 876; resources 447; level6 239; sharedassets1 179; level0 84; level4 34; level5 27; sharedassets6 5 | `python tools/unityfs.py names Monstrum/Monstrum_Data --out FILE`, counted by class and file |
| names | 36,416 read at fixed offsets, 0 by scanning, 22,950 empty, 2 unparsed; 42,284 objects of classes with no fixed name | same |
| names of the game | GameObjects named `Brute` 22, `Hunter` 195, `Fiend` 143, `Monster` 123, with `Deck` 1,680, `Helicopter` 16, `LifeRaft` 20, `Player` 427 | same, `grep -c` |
| MonoScripts | 1,877 in `globalgamemanagers.assets`, 3 in `unity default resources`, 0 in any `level` | same |
| the reference graph | `globalgamemanagers` → 9 files; each `levelN` → `globalgamemanagers.assets` + its `sharedassetsN.assets` + `unity default resources` | `python tools/unityfs.py externals Monstrum/Monstrum_Data` |
| ResourceManager | 34 entries: `video/escape_*` ×9, `srdebugger/*` ×18, 7 shaders/cursors | `python tools/unityfs.py paths Monstrum/Monstrum_Data/globalgamemanagers` |

## The textures and the sidecars

| figure | value | command |
|---|---|---|
| Texture2D + Cubemap | 825 (822 + 3) in 9 files; body closure 825 of 825; arithmetic closure 825 of 825 | `python tools/unitytex17.py census Monstrum/Monstrum_Data` |
| where the pixels are | streamed 737, inline 79, none 9 (the `Font Texture` atlases, 0 × 0) | same |
| formats | DXT5 606 (1,503,573,728 B), RGBA32 82 (64,404,000), ARGB32 42 (933,888), DXT1 39 (24,904,208), ARGB4444 33 (47,714), RGB24 19 (62,840,685), Alpha8 4 (122,904) | same |
| sizes | 1024² ×319, 2048² ×185, 512² ×82, 64² ×51, 256² ×31, 128² ×29, 363² ×24, 1920×1080 ×12, 4096² ×3 | same |
| the sidecars tiled | 7 of 7 `.resS` end where their last record ends: 5, 8, 81, 518, 116, 1, 8 records; gaps 0/0/0/4/4/0/0 bytes; overlaps 0 | `python tools/unitytex17.py sidecars Monstrum/Monstrum_Data` |
| the largest | `Brute_Diffuse_04`, `Player_Skin01_03_Dif`, `Player_Skin01_03_Norm`: 4096², DXT5, 13 mips, 22,369,648 B each | `python tools/unitytex17.py list Monstrum/Monstrum_Data` |
| a render | `python tools/unitytex17.py extract Monstrum/Monstrum_Data/sharedassets3.assets --name Brute_Diffuse_04 --out OUT.png --mip 2` | (renders stay in `_work`; eleven confirmed by the owner) |

## The audio and the movies

| figure | value | command |
|---|---|---|
| banks | 1 + 8 + 2,197 + 9 = 2,215 FSB5 banks in four `.resource`; every chain ends at the file's last byte | `python tools/fsb5.py walk Monstrum/Monstrum_Data/sharedassets{0,1,3,4}.resource` |
| codecs | PCM16 ×2,029, Vorbis ×186; 44,100 Hz ×2,215; mono 1,964, stereo 251; 8,338.24 s = 138.97 min | same |
| clips | 2,216 AudioClip bodies, 2,216 parse; 2,215 with a record, 2,215 matched to a bank; size, channels, frequency, codec agree 2,215 of 2,215; the one without is `MONSTRUM SPLASH SCREEN audio` (0 ch, 0 Hz) | `python tools/fsb5.py clips Monstrum/Monstrum_Data --list` |
| clip names | `ENV_` 980, `MOV_` 648, `ACT_` 440, `ANI_` 35, `DIA_` 31, `MUS_` 24, `UI_` 23, `AMBI_` 17, `Escape_` 9, `ATMO_` 6; `Hunter` in 374 names, `Brute` 91, `Fiend` 66 | same, counted |
| movies | 10 MovieTexture: 9 `Escape_{Heli,LifeRaft,Submersible}_{Fiend,Hunter,Brute}` in `resources.assets` (126,057,812 B of bodies), `MONSTRUM SPLASH SCREEN` in `sharedassets0.assets` | `python tools/unitymovie.py list Monstrum/Monstrum_Data` |
| the Ogg | 127,548,540 bytes, 3,181 pages, 0 bad CRCs, 10 of 10 close; escapes 1920 × 1080 at 25 fps, video only: Heli 54.6 s ×3, LifeRaft 42.8 s ×3, Submersible 46.0 s ×3; splash 10.0 s at 29.86 fps with Vorbis 48 kHz stereo | `python tools/unitymovie.py census Monstrum/Monstrum_Data` |

## The assembly

| figure | value | command |
|---|---|---|
| `Assembly-CSharp.dll` | 2,156,032 B, PE32, CLR v2.0.50727, `csc 8.00`, COFF stamp 0 | `python tools/clrmeta.py Monstrum/Monstrum_Data/Managed --census`; `python tools/pecensus.py Monstrum` |
| heaps | #Strings 268,112 B (18,316 names), #US 141,056 B (3,015 literals), #Blob 70,800, #GUID 16, #~ 551,700 | `python tools/cilmeta.py validate …/Assembly-CSharp.dll` |
| tables | 29 present, 48,025 rows: TypeDef 2,124, Field 11,076, MethodDef 11,433, Param 4,959, MemberRef 3,560, TypeRef 475, CustomAttribute 3,128, Property 1,847 … | `python tools/cilmeta.py census …` |
| the closures | TypeDef/Field/MethodDef/MemberRef rows end inside `#~`; FieldList, MethodList, ParamList monotone; every row named: 15 checks pass | `cilmeta.py validate` |
| namespaces | 42; global 1,510 types, `InControl` 245 + `InControl.NativeProfile` 120, `SRDebugger.*` 101, `SRF.*` 51, `Tobii.*` 55, `XInputDotNetPure` 9 … | `cilmeta.py census` |
| `monsterSeed` | a field of `LevelGeneration` (90 fields, 43 methods), with `selectedMonster`, `chosenMonstType`, `allMonsters`, `monsterSpawnPoints`, `levelSeed` | `python tools/cilmeta.py owners … --grep monsterSeed`; `members … --type LevelGeneration` |
| co-op | literals 0 of 3,015; game types 0 of 2,124; members 0 of 22,509; TypeRefs into Steamworks 9 (stats, API, client), into networking 0; MemberRefs 0 of 3,560 for `lobby\|matchmak\|network\|p2p\|socket\|multiplayer\|coop`; 12 Steamworks calls, all achievements and init | `cilmeta.py owners\|typerefs\|memberrefs` (notes/cilmeta-coop-and-monsters.txt) |
| the other assemblies | 15 managed: Unity's 5, Mono's 5, the studio's 4 (`Assembly-CSharp*`, `Assembly-UnityScript*`), `UnityScript.Lang`; 11 native; 5 of 28 Authenticode-signed | `python tools/pecensus.py Monstrum` |

## The diary, the crash and GOG

| figure | value | command |
|---|---|---|
| the 2026 log | 4,362 lines; 4 runs; seeds 1872376908, 2128268496, 1538225150, 1062072777; counters 2/2/2 → 2/2/3 → 2/3/3 → 2/4/3; Fiend, Hunter, Hunter, Brute; UpperDeck, LowerDeck ×3; 16 keys saved ×4, 0 `True`; 16 clock stamps 17:38:50 → 17:50:30; `Local` achievements; RTX 3060 Ti | `python tools/unitylog.py summary\|runs Monstrum/Monstrum_Data/output_log.txt` |
| the tally rule | 9 of 9 counter steps between runs match "the chosen monster's counter +1" | `unitylog.py runs` |
| the 2018 log | 1,278 lines; 1 run; seed 321071888; counters 19/18/19; Hunter; UpperDeck; stamps 17:07:42 → 17:08:15; 2 crash markers; GTX 970 | `unitylog.py … Monstrum/2018-12-15_165138/output_log.txt` |
| the minidump | 155,324 B; version 0xA0EEA793; 12 streams (8 used); 2018-12-15 16:18:54 UTC; x64, 8 processors, Windows 10.0 build 17134; 32 threads; 38 memory ranges, 85,504 B; 110 modules (7 from the install) | `python tools/mdmp.py info\|streams\|modules Monstrum/2018-12-15_165138/crash.dmp` |
| the exception | 0xC0000005 read at 0x260, address 0x00007FF711D5EC8C = `Monstrum.exe` + 0x7BEC8C (base 0x00007FF7115A0000, 23,150,592 B): the Unity player, not the game's IL | `mdmp.py info` |
| the clocks of the crash | folder `165138`; `error.log` "Error occurred at 2018-12-15_171856"; files 17:18:54 local; dump 16:18:54 UTC | `mdmp.py info`; `notes/mtimes.txt` |
| GOG's manifest | 74 + 1 entries; 73 present (2,494,589,005 B), 2 absent (a hash-named file, `__redist\ISI\scriptinterpreter.exe`), 16 present and undeclared (5,587,552 B); 73 + 16 = 89 | `python tools/gogmanifest.py --root Monstrum` |
| GOG's furniture | `.info`: gameId 1950261263, buildId 51712611558478470, one FileTask `Monstrum.exe`, one URLTask; `.script`: save path `LocalLow/Team Junkfish/Monstrum`; `.hashdb`: a ZIP of one member, 73,932 → 2,423 B; `webcache.zip`: 9 members; `EULA.txt`: GOG's User Agreement, 38,181 B | `python tools/zipdir.py …`; the files, read |
| Inno | `unins000.exe` 1,343,048 B, Inno Setup 5.6.2 stub, no SetupLdr table (refused in one line); `unins000.dat` `Inno Setup Uninstall Log (b)`; `unins000.msg` `Inno Setup Messages (5.6.2) (u)` | `python tools/inno56.py ldr Monstrum/unins000.exe`; `coverage.py tree` |

## The tools

| figure | value | command |
|---|---|---|
| the box | 609 Python files: 605 inherited from `pc-bumpy-doc` (7 changed here), 4 written | `python tools/toolsdiff.py ../pc-bumpy-doc/tools` (notes/toolsdiff.txt) |
| selftests | 15 tools, 0 failures, `PYTHONIOENCODING` unset | notes/selftests.txt |
| dirguard | 608 surveyed: raised 214, refused 317, exit 0 77 | `python tools/dirguard.py --survey --tools tools` |
| rule 0 | see notes/rule0-report.txt | `python tools/rule0hook.py --report` |
