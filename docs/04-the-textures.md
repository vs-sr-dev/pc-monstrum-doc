# 04 — The textures: 825 objects at a serialization version the box had never decoded, two closures on every one, and eleven renders — the ship, the three monsters, the three escapes, the studio — confirmed by the owner at sight

*Measure: `python tools/unitytex17.py census Monstrum/Monstrum_Data` and `sidecars` (notes/unitytex17-census.txt), `python tools/unitytex17.py list Monstrum/Monstrum_Data` (notes/unitytex17-list.txt), `python tools/unitytex17.py extract FILE --name NAME --out OUT.png [--mip N]` (the PNGs stay in `_work\` and are not published), `python tools/unitytex17.py selftest` (notes/selftests.txt).*

## The layout, read before a pixel was claimed

The box had `unitytex.py`, written on `pc-iamsetsuna-doc` for serialization
version 15 (Unity 5.2), where a `Texture2D`'s pixels follow its header
inside the object body. Here the version is 17 (Unity 5.5), and the
pre-briefing's guess was "a field or two around `m_StreamData`". A hex dump
of the first three bodies in `sharedassets0.assets` (`UISprite`, `Background`,
`Knob`) settled it field by field:

    aligned string  m_Name
    i32 × 5         m_Width, m_Height, m_CompleteImageSize, m_TextureFormat, m_MipCount
    u8, u8, align   m_IsReadable, m_ReadAllowed
    i32 × 4, f32    m_ImageCount, m_TextureDimension, m_FilterMode, m_Aniso, m_MipBias
    i32 × 3         m_WrapMode (one; U/V/W came in 2017), m_LightmapFormat, m_ColorSpace
    i32 + bytes     imageDataSize, the inline pixels (0 bytes on 743 of 822)
    u32, u32, str   m_StreamData: offset, size, path  -- e.g. `sharedassets2.assets.resS`

Identical to version 15 up to `imageDataSize`, then one record more. A
`Cubemap` (class 89) is the same body followed by `m_SourceTextures` (a count
and six 12-byte PPtrs), and its stream holds `m_ImageCount` faces of
`m_CompleteImageSize` each. `unitytex17.py` reads this and asks two closures
of every object:

1. **the body end**: the last byte of the path plus 0–3 bytes of alignment
   must be the last byte of the body — a layout error anywhere above moves it
   by more than three;
2. **the arithmetic**: `m_CompleteImageSize` must equal width × height ×
   bits-per-pixel summed over every mip level (blocks of 4 × 4 at 8 or 16
   bytes for DXT1/DXT5), and the stream record's size must equal it too.

**825 of 825 close on both** (822 `Texture2D` + 3 `Cubemap`). The nine that
first failed the arithmetic were the `Font Texture` objects — 0 × 0, dynamic
font atlases with no pixels at build time, which a reader that rounds a
level up to 1 × 1 gets wrong by four bytes; a 0 × 0 texture is 0 bytes. The
second closure is also what settled a table: `unityfs.py` named format 10
`DXT3`; at DXT3's 8 bits per pixel none of the 39 textures of that format
would close, at DXT1's 4 bits all 39 do, and Unity's enum has no DXT3.
Repaired ([08](08-tools-predictions-and-calibration.md)).

## The census

| | |
|---|---|
| objects | 825 in 9 files: `sharedassets2.assets` 516, `sharedassets3` 117, `sharedassets1` 82, `level3` 40, `unity default resources` 39, `resources.assets` 10, `sharedassets0` 9, `sharedassets6` 8, `sharedassets5` 1 (+ the cubemaps: 1 in `sharedassets1`, 2 in `sharedassets2`) |
| where the pixels are | streamed 737 (into 7 `.resS`), inline 79 (`level3`'s 40 UI textures, the engine's 38, one in `sharedassets3`), none 9 |
| bytes | stream records 1,657,079,064 + 8 bytes of alignment = the seven sidecars, 1,657,079,072; inline 1,147,663; the three cubemaps' records hold six faces each (1,679,520 B) where `m_CompleteImageSize` counts one (279,920) — so the census's sum of `m_CompleteImageSize`, 1,656,827,127, is the records less five faces plus the inline bytes, and the arithmetic closes |
| formats | DXT5 606 (1,503,573,728 B, 90.75 %), RGBA32 82 (64,404,000), ARGB32 42 (933,888), DXT1 39 (24,904,208), ARGB4444 33 (47,714), RGB24 19 (62,840,685), Alpha8 4 (122,904) |
| sizes | 1024² ×319, 2048² ×185, 512² ×82, 64² ×51, 256² ×31, 128² ×29, 363² ×24 (the `UI_Interactable_*` icons), 1920 × 1080 ×12, 1024 × 512 ×10, 4096² ×3 |
| the largest | `Brute_Diffuse_04`, `Player_Skin01_03_Dif`, `Player_Skin01_03_Norm`: 4096², DXT5, 13 mips, 22,369,648 B — the selftest's arithmetic check is this number |

The names say what the game is made of, before any pixel: 252 begin `Upd_`
and 40 `Lwd_` (the two decks: `Upd_Security_SecurityMonitors_01_Dif`,
`Upd_Mess_Plate_Small_01_Norm`, `Upd_Rec_BookShelf01_02_Dif`,
`Lwd_Container_NanaDollFace_01_Dif`), 118 `Misc_`, 57 `ProGen_`, 40 `Key_`
(`Key_FlareGun01_01_Dif`, `Key_WalkieTalkie01_01_Norm`, `Key_DuctTape01_02_Norm`,
`Key_LifeRaft01_02_Norm_Broken`, `Key_BoltCutters01_01_Dif`, `Key_WeldingKit01_02_Norm`
— the items), 33 `UI_`, 21 `ShipExterior_`, 20 `Menu_`, 11 `Story_`, 7
`Heli_`, 9 `Font`. Two monsters carry numbers, not names: `Monster02_*` (five
textures, inside and outside) and `Monster03_*` (five, with `_Eyes_`); the
third is named, `Brute_Diffuse_04` and `Brute_Emissive_01`. The object ties
the numbers to the names itself: the mesh and GameObject
`Hunter_Rig_Base:Monster02_LowRetop05` and the GameObject
`Fiend_Base_Rig:Monster03_Fiend_01` ([03](03-the-serialized-files-and-their-sidecars.md)).

## The renders

Eleven textures were decoded to PNG — DXT5 through `unitytex.py`'s block
decoder, RGB24 and RGBA32 linearly, all flipped from Unity's bottom-up rows —
and every one was sent to the owner as it came out. **All eleven were
confirmed at sight**, in three messages; the owner's own words for two of
them are in the table. The PNGs are not published; the object is described.

| texture | size, format, level decoded | what it paints | owner |
|---|---|---|---|
| `Menu_Loading_HintBackground02_01_Dif` | 1920 × 1080 RGB24, mip 0 | a loading hint: a fuse box marked `FUSE` with ON/OFF buttons, a blowtorch in the player's hand, a dim corridor behind glass, all inside a rust-red frame | confirmed |
| `Brute_Diffuse_04` | 4096², DXT5, mip 2 (1024²) | the Brute's skin unwrapped: charred black-and-red flesh with incandescent orange openings, the jaw and eyes at the top centre | confirmed |
| `Monster02_Outsides01_03_DifOpac` | 2048², DXT5, mip 1 | the second monster's hide | "mostro 02 Hunter" |
| `Monster03_02_Dif` | 2048², DXT5, mip 1 | the third monster's skin | "mostro 03 Demon" — the assembly's name for it is `Fiend` |
| `Death0` | 1920 × 1080 RGB24 | a corridor of pipes, a blood spray on the wall, a body on the floor | confirmed |
| `Win` | 1920 × 1080 RGB24 | `YOU ESCAPED` in capitals over a red life raft, the container ship's red lights in the fog behind | confirmed |
| `Untitled` | 1920 × 1080 RGB24 | a blue-and-orange helicopter lifting away from the deck at night, its searchlight on | confirmed |
| `Win Screen Sub` | 1920 × 1080 RGB24 | a bathysphere-like submersible under water, two lamps lit, bubbles rising | confirmed |
| `JunkfishLogo_Transparent_571_475` | 571 × 475 RGBA32 | a mechanical angler-fish with a gear for a lure over the word `JUNKFISH` — the studio | confirmed |
| `Story_Note_ChefToQM` | 2048², DXT5, 1 mip | a taped, lined note: *Stuart, we've got a big rat problem on board … that idiot Santos … Ocampo should have the list … Or we can have stuffed rats at sea. Your call. Mills* | confirmed |
| `Menu_Options_Main01_01_Dif` | 2048², DXT5, mip 1 | crumpled paper with two gear icons and ruled lines — the menu's ground; the title is not a texture | confirmed |

So the three escapes are three win screens (`Win` = life raft, `Untitled` =
helicopter, `Win Screen Sub` = submersible) matching the three literals
`ESCAPE_LIFERAFT`, `ESCAPE_HELICOPTER`, `ESCAPE_SUBMARINE` and the nine
movies of [05](05-the-audio-and-the-movies.md); the three monsters are three
skins; the ship is 252 upper-deck and 40 lower-deck sheets and six
1920 × 1080 loading hints (`Menu_Loading_HintBackground02` to `07`); and
the four crew names on the note — *Stuart*,
*Santos*, *Ocampo*, *Mills* — occur in no string of the assembly (0 of 3,015
literals, 0 of 18,316 names): they are pixels. Eleven `Story_Note_*` textures
exist; one was read.

## What the render decided

A picture is the only proof that a container was read for its *contents*
and not merely for its framing. The first two renders were sent before the
audio or the assembly were touched, because a wrong layout produces a
plausible table and a wrong stride produces a picture nobody recognises; the
owner knows this ship with their hands, and recognised a fuse box and a
monster's hide at the first look. What the render could not decide, and the
closures could, is the count: 825 objects were not rendered, and the claim
that they *would* decode rests on the two closures holding on every one and
the same decoder having drawn eleven of them.

## What is not measured

* 814 of 825 textures, not rendered — the closures cover them, the eye does
  not;
* the normal maps (`_Norm`), the emissive and specular sheets (`_Emissive`,
  `_SGR`): decoded as colour if asked, meaningless as a picture;
* the ten remaining `Story_Note_*` (`Voices01`–`03`, `Expedition01`–`04`,
  `DocReport`, `FredToKim`, `QMToChef`) and the five other loading hints, which
  are the story of the ship and were left to the owner;
* mip levels above 0 of the 4096² sheets: `--mip 2` was decoded for time
  (a million 4 × 4 blocks in pure Python is a minute; a quarter-size level is
  four seconds), and the level is printed with the render, never hidden;
* the three cubemaps' faces; the 79 inline textures' pictures (UI and the
  engine's).
