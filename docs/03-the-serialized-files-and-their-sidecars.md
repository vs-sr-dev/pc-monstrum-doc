# 03 — The serialized files and their sidecars: nineteen containers of 101,652 objects, seven scenes, a ship named room by room, and 2.1 GB of pixels and samples tiled to the last byte by the records that point at them

*Measure: `python tools/unityfs.py census|verify|externals|names|paths Monstrum/Monstrum_Data` (notes/unityfs-census.txt, unityfs-verify.txt, unityfs-externals.txt, unityfs-info.txt), `python tools/unitytex17.py sidecars Monstrum/Monstrum_Data` (notes/unitytex17-census.txt), `python tools/fsb5.py walk …` and `clips` (notes/fsb5-audio.txt), `python tools/strdump.py Monstrum/Monstrum_Data/globalgamemanagers`.*

## The container

A Unity 5.5 player build keeps its content in **SerializedFiles**: a
big-endian header (metadata size, `fileSize`, version, data offset,
endianness), the engine version as a C string at +20, a type table, an object
table (path ID, start, size, type), a script-type table and an external
table. The format is Unity's and Unity has never published it; the public
accounts are third parties' (AssetStudio, UnityPy, disunity), and the reader
here is `unityfs.py`, written on `android-talesofluminaria-doc` for version 21
and reading version 17 unchanged. The filing is therefore *decoded*, not
*specified* ([01](01-what-this-is.md)).

Nineteen of them, all version 17, **type tree absent** (a player build
carries none), target platform 19, little-endian:

| file | bytes | objects | what it is |
|---|---:|---:|---|
| `globalgamemanagers` | 275,844 | 20 | the managers: `PlayerSettings` (`Team Junkfish`, `Monstrum`, bundle id `com.oculus.UnitySample`), `BuildSettings` (the seven scenes), `ResourceManager` (34 paths), `InputManager`, `TagManager`, `MonoManager` … |
| `globalgamemanagers.assets` | 247,696 | 1,880 | 1,877 `MonoScript` objects (of 1,880 in all) — the class names the MonoBehaviours point at |
| `level0` … `level6` | 5,331,632 | 18,443 | the seven scenes |
| `resources.assets` | 126,786,076 | 2,203 | nine `MovieTexture` (126,057,812 B, 99.43 % of the file), five fonts, 447 GameObjects |
| `sharedassets0` … `sharedassets6.assets` | 170,646,136 | 79,018 | the assets the scenes share: 822 textures' headers, 2,216 `AudioClip` records, 1,157 meshes (133 MB, inline), 388 animation clips, 703 materials |
| `unity default resources`, `unity_builtin_extra` | 1,130,952 | 88 | the engine's own (the first says `5.5.0b10`) |
| | **304,418,336** | **101,652** | |

`fileSize` agrees with the file on 19 of 19; 101,652 of 101,652 objects lie
inside the file that declares them (`unityfs.py verify`). The object bodies
sum to 302,012,975 bytes; the 2,405,361 between that and the files are
headers, tables and alignment.

The class census, the whole of it in `notes/unityfs-census.txt`: 28,979
`GameObject`, 26,577 `Transform`, 15,773 `MonoBehaviour`, 5,405
`BoxCollider`, 2,216 `AudioClip`, 1,880 `MonoScript`, 1,157 `Mesh`, 822
`Texture2D`, 703 `Material`, 388 `AnimationClip`, 129 `Shader`, 10
`MovieTexture`, 9 `Font`, 3 `Cubemap`. By bytes the meshes are 44.08 % of the
object bodies and the ten movies 42.23 %; every other class together is
13.69 %.

## The seven scenes

`BuildSettings` in `globalgamemanagers` lists them at 0xE310 in build order,
and `unityfs.py externals` says what each pulls in:

| level | scene | GameObjects | draws on |
|---|---|---:|---|
| `level0` | `Assets/Scenes/Splash.unity` | 84 | `sharedassets0` |
| `level1` | `Menus.unity` | 876 | `sharedassets0`, `sharedassets1` |
| `level2` | `Loading.unity` | 1,825 | `sharedassets2` |
| `level3` | `MainSecondary.unity` | 2,288 | `sharedassets0`–`3` |
| `level4` | `CompletionMovieScene.unity` | 34 | `sharedassets0`, `1`, `4` |
| `level5` | `Credits.unity` | 27 | `sharedassets0`, `5` |
| `level6` | `Collectables.unity` | 239 | `sharedassets0`–`3`, `6`, `resources.assets` |

Every level also references `globalgamemanagers.assets` and `unity default
resources`. `level3` is the game: the eight GameObjects each named `Brute`,
`Hunter` and `Fiend` are there, with `BruteCamPosition`, `Ctrl_Monster_Rig`,
the whole `Hunter_Rig_Base:*` skeleton (`Spine_Joint_01`–`08`, `R_Wrist_Joint`,
`R_Toe_Joint` …) and `Fiend_Base_Rig:Monster03_Fiend_01`. The ship itself is
mostly *not* in the level: it is 15,140 GameObjects in `sharedassets2.assets`
and 7,835 in `sharedassets3.assets` — prefabs, which `LevelGeneration`'s
fields (`roomPrefabs`, `corridorPrefabs`, `stairPrefabs`, `ventPrefabs`,
`cargoRoomPrefabs`, `engineRoomPrefabs`, `deckPrefabs` …) assemble at run
time from a seed ([06](06-the-assembly-and-the-co-op.md)). The scene of the
ship is generated; the log's `Test Seed` is its input
([07](07-the-diary-the-crash-and-gog.md)).

## The ship, by name

36,416 names were read at fixed field offsets, none by scanning
(`unityfs.py names`; 22,950 are the empty string most components carry, 2
did not parse, and 42,284 objects belong to classes with no fixed name —
Shader, Transform, the colliders). The 28,979 GameObject names, cut at the
first `_`, space or `:`:

    Lwd 3,432   ProGen 2,822   Upd 2,565   Misc 1,605   Cube 1,178   Deck 865
    Rm 723   Text 506   PatrolPoint 494   navOcclusion 391   Book 345
    Box02 319   Paper 278   Can02 262   CargoContainer 198   Hunter 179
    HandIK 150   SmallKeyItemPlaceholder 139   Fiend 130

`Lwd` and `Upd` are the lower and upper decks (the log's `LowerDeck` and
`UpperDeck`); `ProGen` is procedural generation; `Rm` the rooms;
`PatrolPoint` ×494 is where a monster walks; `Deck`, `CargoContainer`, `Book`,
`Paper`, `Can02` are the furniture. Substrings, over all 36,416 names: `Deck`
1,890, `Hunter` 667, `Fiend` 289, `Monster` 271, `Brute` 166, `Helicopter` 25,
`LifeRaft` 43. The escapes are GameObjects too: `Helicopter` ×16 and
`LifeRaft` ×20 as whole names.

## The sidecars

The 19 files hold the *headers* of 822 textures and 2,216 clips; the pixels
and the samples are outside them, in eleven files with no magic of their own:

* **`.resS` ×7, 1,657,079,072 bytes.** Each `Texture2D` (and each of the three
  `Cubemap`) ends with `m_StreamData` — u32 offset, u32 size, the sidecar's
  name ([04](04-the-textures.md)). Sorted by offset, the 737 records **tile
  every sidecar to its last byte**: `sharedassets2.assets.resS` (1,117,031,056
  B, 44.68 % of the object) is 518 records with 4 bytes of alignment in 4
  gaps; `sharedassets3` 116 records, 4 bytes in 2 gaps; `sharedassets1` 81
  records (the 81st is `KitchenCubeMap2`, six faces of 16,384 bytes, which is
  what closed the last 98,304 bytes); `resources`, `sharedassets0`, `5`, `6`
  with 5, 8, 1, 8 records and no gap. No overlap anywhere. The meshes do
  **not** stream: all 1,157 close their `m_StreamData` with an empty path,
  and their 133 MB are inside the `.assets`.
* **`.resource` ×4, 492,065,344 bytes.** Each `AudioClip` ends with
  `m_Resource` — the sidecar's name, u64 offset, u64 size — and the sidecar
  is **one FSB5 bank per clip laid end to end**: 1 + 8 + 2,197 + 9 = 2,215
  banks, each chain ending at the file's last byte (`fsb5.py walk`), each
  bank claimed by exactly one clip with its size, channels, frequency and
  codec agreeing 2,215 of 2,215 (`fsb5.py clips`;
  [05](05-the-audio-and-the-movies.md)).
* **The movies are not in a sidecar.** Unity 5's `MovieTexture` holds its Ogg
  in the object body, which is why `resources.assets` is 127 MB
  ([05](05-the-audio-and-the-movies.md)).

This is the fact that repairs the coverage table: a `.resS` begins with
whatever its first texture's first block is (sixty-four zero bytes on
`sharedassets2`) and can be filed only by what *names* it. `coverage.py`'s
tree pass now asks the sibling serialized files for their stream records and
files a sidecar as *derived* when every record lies inside it
([08](08-tools-predictions-and-calibration.md)).

## What `ResourceManager` knows

34 entries, every one in file 8 (`resources.assets`): `video/escape_heli_brute`
… `video/escape_submersible_hunter` (the nine movies, path IDs 31–39),
eighteen `srdebugger/ui/prefabs/*` (a debug console shipped in the build),
`simplecursor`, `shadow-screenblurrotated`, three shaders and two VR GUI
surfaces. The assembly's 154 `Noises/…` (144) and `Music/…` (10) literals are **not**
in it: they are keys of the game's own `audioLibraries` (a field of
`LevelGeneration`), not `Resources.Load` paths, and the pre-briefing's reading
of them as the latter is corrected in [08](08-tools-predictions-and-calibration.md).

## What is not measured

* the MonoBehaviour bodies (15,773, 4.6 MB): without a type tree their fields
  are the assembly's to describe, and the assembly was read to the member
  names, not the IL ([06](06-the-assembly-and-the-co-op.md));
* the meshes, the animation clips, the materials, the shaders, the 5,405 box
  colliders: counted, not read;
* the `level3` object graph — which prefab goes where is decided at run time
  by `LevelGeneration` and is not in any file;
* the 2 names that did not parse, and the names of the 42,284 unnamed
  objects (the shaders' are in the `ScriptMapper`, `unityfs.py shaders`, not
  run here).
