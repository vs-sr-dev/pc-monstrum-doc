# 08 — Tools, predictions and calibration: a coverage table repaired before anything else, seven inherited tools changed and four written, five hunches scored, nine corrections, and a term of +7.00 on a series of forty-seven

*Measure: `python tools/toolsdiff.py ../pc-bumpy-doc/tools` (notes/toolsdiff.txt), the fifteen selftests with `PYTHONIOENCODING` unset (notes/selftests.txt), `python tools/dirguard.py --survey --tools tools` (notes/dirguard-survey.txt), `python tools/rule0hook.py --report` (notes/rule0-report.txt), `python _work/calib3.py` (the series; `_work\` is not published, the numbers are here).*

## The box

609 Python files in `tools\`: the 605 of `pc-bumpy-doc`, copied whole and
proved so before anything was touched (`toolsdiff`: 605 common, 0
differing), of which **seven were changed here** and **four were written**.
Every new file name was checked to be free first — and two of the names the
pre-briefing proposed were *not* free, which is a finding: `fsb5.py` existed
(Luminaria's) and `cilmeta.py` existed (Murder of Sonic's) and both did most
of what the pre-briefing said the box could not do. They were extended, not
duplicated.

| tool | what changed, and why |
|---|---|
| `coverage.py` | **the repair** (below): seven probes and a tree-level sidecar pass; selftest 187 → 220 checks |
| `clrmeta.py` | `nameguard.guard()` and `dirguard.want_file`: `--userstrings` died of `UnicodeEncodeError` on this heap's ninth literal when `PYTHONIOENCODING` was unset, and the pre-briefing had set the variable instead of fixing the tool; the selftest now asserts the guard |
| `cilmeta.py` | Field, MethodDef, Param, InterfaceImpl and MemberRef rows; `members`, `owners`, `typerefs`, `memberrefs`; seven checks and a hand-built table stream in the selftest (5 → 12 cases) |
| `fsb5.py` | `walk` (banks laid end to end, chain closure) and `clips` (AudioClip records at version 17 matched to banks, four agreements); guards; selftest 9 → 18 checks |
| `inno56.py` | an `InnoError` reaching `main` is one line and exit 1, not a traceback with this machine's paths in it |
| `unityfs.py` | `TEXFMT`/`BPP`/`BLOCK` had 8 = R16, 9 = DXT1, 10 = DXT3 — off by one against Unity's enum (9 R16, 10 DXT1, no DXT3); `textures` refuses a file older than the 2019.4 layout it reads instead of tabulating "format 0, 39 bytes" with rc 0, which is what it did here |
| `pe.py` | a bare call printed nothing and returned 0; it prints usage and returns 2 |
| **`unitytex17.py`** | Texture2D and Cubemap at serialization version 17 with `m_StreamData` into `.resS`; two closures; `list`, `census`, `extract --mip`, `sidecars`; the decoders and the PNG writer imported from `unitytex.py`; 17 checks |
| **`mdmp.py`** | a Windows minidump's header, stream directory, module list, exception record, system info, from `minidumpapiset.h`; directories masked; 12 checks on a hand-built dump |
| **`unitylog.py`** | the Unity player log and MONSTRUM's lines in it: runs, seeds, counters, monster, deck, keys, stamps, the tally rule; paths masked; 10 checks |
| **`unitymovie.py`** | `MovieTexture` bodies sliced to Ogg and walked with `oggcensus.py`; Theora and Vorbis identification headers, durations both ways; 11 checks |

Each new tool has `dirguard.want_file` or `want_tree`, `nameguard.guard()`,
a selftest on hand-built specimens that passes in a repository without the
object, a refusal for every closure it checks, and file selection by magic
or by the object tables — never by `.resource`, `.dmp` or `.assets`. Every
patch to an inherited tool was a script writing a temporary file and
`os.replace()`; `inno56.py` came CRLF and left LF, `unityfs.py` came CRLF
and stayed CRLF.

## 0. The repair, first

`coverage.py tree --root Monstrum` filed 35 files, **98.2831 %** of the object,
as opaque: it had a probe for a PE, a ZIP, an icon, a BMP and eight text
codecs, and none for a Unity SerializedFile, a `.resS`, an FSB5 bank, a
minidump, a Mono `.mdb`, a Shell Link or Inno's `.dat`/`.msg` — while
`unityfs.py`, which reads the first, and `fsb5.py`, which reads the third,
were in the same directory. Three Unity objects before this one had each
carried their own coverage tables; this box's had never learned the format.
A denominator that says 98 % opaque cannot be cited by a chapter that then
reads 86 % of the bytes, so the probes came before the first reader:

* `_unity_serialized` — version 5..30 (the 64-bit header of 22+ read too),
  endianness byte 0/1, dataOffset inside fileSize, an engine-version string
  of digits, dots and `abfpx`, and **`fileSize == the file** — Unity's own
  number, the free positive control; *decoded*, because Unity never published
  it and AssetStudio, UnityPy and disunity did;
* `_fsb5` — magic, version 0/1, codec ≤ 17, at least 8 header bytes per
  sample, the first bank inside the file; the chain closure is `fsb5.py
  walk`'s and the docstring says so; *decoded* (FMOD's, read by python-fsb5
  and vgmstream);
* `_mdmp` — `MDMP`, the low version word 0xA793, a directory past the
  32-byte header, every listed stream inside the file; *specified*
  (Microsoft's SDK header);
* `_mono_mdb`, `_shllink`, `_inno_unins_log`, `_inno_msgs` — magic and
  version, header size and CLSID, the two 64-byte IDs with their NUL pad
  clean; *specified*, with the note that Mono's and jrsoftware's formats are
  published as source rather than as a document;
* **the sidecar pass** — a `.resS` has no magic (`sharedassets2`'s first
  sixty-four bytes are zero) and can be filed only by what names it:
  `tree_rows` now asks the sibling serialized files for every `m_StreamData`
  (Texture2D, Mesh: two words before the path) and `m_Resource` (AudioClip:
  two words after it) record naming a `.resS` or `.resource`, and files the
  sidecar as *derived* when every record lies inside it. The pass is
  injectable, so the selftest exercises both directions without an object.

Thirty-three checks were added, each with a negative control built to be a
near miss: a fileSize one byte off, a version-22 header with the sizes moved,
a bank one byte past the file, a stream past the end, a version-49 `.mdb`,
another CLSID, a dirty pad, a sibling's name without its length prefix, a
record one byte past its sidecar. After it: specified 59 files 1.8644 %,
decoded 23 files 31.8571 %, derived 7 files 66.2785 %, **opaque 0 files, 0
bytes**, residue 0 ([01](01-what-this-is.md)). The buckets are not summed
into a coverage figure; the derived one carries this session's warrant only.

## Rule 0

`tools/rule0hook.py` is registered in `.claude\settings.local.json` (not
published). It was made to refuse something before the first tool was
touched — a `python -c` — and refused twice more without being asked: a
`sed -i` on a scratch script, and a `python -c ""` left in a compound
command. **164 shell calls seen, 161 allowed, 3 refused, 0 reached the
shell** (notes/rule0-report.txt, written as the last command before the
final commit; the commit's own calls come after it). The habit the prompt named — `python -`
with nothing on stdin — did not fire this time.

`dirguard --survey`: 608 surveyed (the survey skips itself), raised 214,
refused 317, exit 0 77. The four new tools and the seven changed ones all
refuse a directory; the 214 tracebacks are the inherited box's, one fewer
than Bumpy's 215 because `inno56.py` now refuses.

Fifteen selftests were run once with `PYTHONIOENCODING` unset, 0 failures;
`clrmeta.py`'s is the one that would have failed that test before.

## The five hunches of the pre-briefing, scored

*P.7 of `question.txt`, not priced.*

**a.** *The co-op is not in this build: the tables show no Steam lobby or
networking type of the game's own, the literals stay at zero; the owner
remembers another game or a later one.* — Zero at five levels, with the
nuance the hunch did not have: the Steamworks.NET library beside the game
*does* carry 23 lobby types and 214 members, and the game imports none of
them. The last clause is not the object's to say and was not looked up.
**Two of three, the third unmeasured.**

**b.** *Version 17 differs from 15 by a field or two around `m_StreamData`
and decodes once read; mostly DXT1/DXT5; the corridors, the skins and the UI
come out as PNGs the owner recognises at first sight.* — Exactly one record
more; 825 of 825 close; DXT5 606 + DXT1 39 of 825; eleven renders, all
confirmed, the first two before any other reader ran. **Whole.**

**c.** *The FSB5 samples are Vorbis; their names are the `Noises/…` paths'
last components; 2,216 clips over four files, the biggest file holding the
monster sounds.* — PCM16 on 2,029 of 2,215, Vorbis on 186; the banks carry
no names at all and the clips' names are `ENV_`/`MOV_`/`ACT_` codes, not
path components; 2,216 clips, 2,215 banks, four files, and `sharedassets3`
holds 2,197 of them with `Hunter` in 374 names. **Half.**

**d.** *The ten movies are the intro and the three-plus-three endings, Theora
at 1280 × 720 or less.* — Nine endings, three by three, not six; a ten-second
splash, not an intro; Theora, yes; 1920 × 1080, not 1280 × 720. **One and a
half of four.**

**e.** *The monster is chosen by a seed from the clock with per-monster
weights that the `Brute k` counters are; the owning class is a manager
MonoBehaviour in `globalgamemanagers.assets` or `level0`; the crash is in the
player, not the IL.* — The counters are a tally of runs per monster (9 of 9
steps), whether they weight anything is in a method body; the class is
`LevelGeneration`, whose script is in `globalgamemanagers.assets` with every
other; the crash is at `Monstrum.exe + 0x7BEC8C`, in `.text`. **Half.**

Five hunches: one whole truth — the one about the render — two halves, a
two-thirds and a three-eighths.

## The pre-briefing's figures, re-counted

Corrected, each by a command in [02](02-the-technical-sheet.md):

1. "the box does not read the metadata tables" — `cilmeta.py` read Module,
   TypeRef and TypeDef; it did not read Field and MethodDef, which is what
   was added;
2. "an FSB5 census is missing from the box" — `fsb5.py` was in it; the walk
   over a file of banks was missing;
3. "`bfflic.write_png` is 8-bit palette; a true-colour writer is ten lines
   more" — `unitytex.py` had an RGBA writer and the DXT decoders; nothing was
   rewritten;
4. `resources.assets` "carries 2,696 `OggS` page markers" — 2,832, and 349
   more in `sharedassets0.assets`; 3,181 pages over ten movies;
5. "10 MovieTexture (127.5 MB, all in `resources.assets`)" — nine there,
   126,057,812 bytes of bodies; the tenth, the splash, in `sharedassets0.assets`;
6. "`sharedassets3.resource` holds 2,196 FSB5 headers" — 2,197 banks,
   claimed by 2,197 clips;
7. "the `Noises/…` literals are `Resources.Load` paths that name what is in
   `resources.assets`" — the `ResourceManager` has 34 entries and not one of
   them; they are keys of the game's own audio libraries;
8. "Texture2D objects carry `m_StreamData` … Meshes too" — all 1,157 meshes
   close with an empty path; the `.resS` hold textures and cubemaps only;
9. "`unityfs.py scripts` found 0 MonoScripts in `level3`: find out whether
   v17 hides them" — nothing is hidden: 1,877 are in `globalgamemanagers.assets`,
   3 in `unity default resources`, none in any level.

And two hedges resolved rather than corrected: "the owner's — or a previous
owner's — crash" is the owner's (same install folder, a tally at 19/18/19),
and "`.hashdb`, a format to look at" is a ZIP. Nine, against Bumpy's twelve
and Polanie's fourteen.

## Calibration

The series had forty-six terms, carried by hand from `pc-bumpy-doc` with its
+9.00 appended; `calib3.py` printed `claims wrong : 0 of 8` on it before
anything was trusted. What was expected: a coverage table repaired, a
version-17 texture rendered and recognised, the assembly read to its classes,
the co-op answered with counts. What was found: all four — and 825 of 825
textures closing twice with seven sidecars tiled to the last byte; eleven
renders confirmed, the three monsters named by the object's own mesh and
GameObject names as the owner had named them; 2,215 banks matched to 2,215
clips four ways; nine escape films with their audio to the tenth of a
second; the crash located in the player; the tally rule 9 of 9; a 2018 log
that made the crash the owner's at run 57; and two tools the pre-briefing
said were missing that the box already had. A rich haul with no lock on it,
which the pre-briefing had foreseen ("2.5 GB means large, and large does not
mean far") — smaller than Bumpy's codec-and-crack and Polanie's hidden
engine, larger than a census. **The term is +7.00**, the eighth positive in a
row:

    terms 47   sum 16.57   mean 0.3526   negative 27   positive 19   zero 1
    last ten: 53.00, mean 5.3000   tail run of negatives: 0
    the term ranks 14 of 47 by absolute value

`calib3.py --append 7.00` printed it; the file was edited by hand with the
comment above, and the eight claims now state the 47-term series, `claims
wrong : 0 of 8`.

**P20, P21 and P22 remain in declared pause.**

## What this session leaves unmeasured

* the IL of 11,433 methods, the `Constant` table, the signatures
  ([06](06-the-assembly-and-the-co-op.md));
* 814 textures not rendered, the normal maps, ten story notes, five loading
  hints ([04](04-the-textures.md));
* the samples and the frames ([05](05-the-audio-and-the-movies.md));
* the meshes, animations, shaders, materials, the level-3 graph
  ([03](03-the-serialized-files-and-their-sidecars.md));
* the minidump's contexts and memory, the `.mdb`'s tables, `unins000.dat`'s
  records, the `.hashdb` member ([07](07-the-diary-the-crash-and-gog.md));
* `unityfs.py`'s "metadata size 1292 (parsed to 1312)" line — the 20-byte
  header, counted once by the file and once by the reader; not a defect,
  not tidied.
