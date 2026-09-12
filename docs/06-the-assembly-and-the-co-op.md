# 06 — The assembly and the co-op: 2,124 types read to their fields and methods, the class that owns the seed, the enum that names the three monsters, and a co-op that is nowhere in five tables

*Measure: `python tools/cilmeta.py validate|census|types|members|owners|typerefs|memberrefs Monstrum/Monstrum_Data/Managed/Assembly-CSharp.dll` (notes/cilmeta-census.txt, cilmeta-types.txt, cilmeta-members.txt, cilmeta-coop-and-monsters.txt), `python tools/clrmeta.py Monstrum/Monstrum_Data/Managed --census` (notes/clrmeta-census.txt), `python tools/clrmeta.py … --userstrings` (notes/clrmeta-userstrings.txt), `python tools/pecensus.py Monstrum` (notes/pecensus.txt).*

## The programs

`Monstrum.exe` is not the game. It is Unity 5.5.0f3's Windows 64-bit
player — PE32+, linker 10.00, COFF stamp 2016-11-24 14:59:22 UTC, 22,161,920
bytes, twelve sections, `.text` 17.2 MB, 19 import DLLs and none of them
Direct3D (loaded at run time; the log says `Direct3D 11.0 [level 11.0]`) — the
same executable every Unity 5.5.0f3 game on Windows ships, with the game's
icons in its resources. The game is managed code under `Managed\`, run by
`Mono\mono.dll` (2,617,280 B, Unity's build of Mono, Authenticode-signed):

| assembly | bytes | compiler, COFF | types | fields | methods |
|---|---:|---|---:|---:|---:|
| `Assembly-CSharp.dll` — the studio's C# | 2,156,032 | `csc 8.00`, stamp 0 | 2,124 | 11,076 | 11,433 |
| `Assembly-CSharp-firstpass.dll` — the plugins' C# (Steamworks.NET, InControl's native side) | 291,328 | `csc 8.00`, stamp 0 | 410 | 1,891 | 2,386 |
| `Assembly-UnityScript-firstpass.dll` — UnityScript (character motor, movement) | 87,552 | `csc 6.00`, 2017-05-15 | 78 | 645 | 282 |
| `Assembly-UnityScript.dll` — UnityScript (`FirstPersonControl`, `CameraRelativeControl`, `rotateheat` …) | 20,480 | `csc 6.00`, 2017-05-15 | 16 | 145 | 66 |

plus Unity's five (`UnityEngine.dll`, `.UI`, `.Networking`, `.VR`,
`.PlaymodeTestsRunner`), Mono's five (`mscorlib`, `System`, `System.Core`,
`Mono.Security`, `Boo.Lang`) and `UnityScript.Lang`: fifteen managed, eleven
native (`pecensus.py`: 28 PE files, 16 PE32 and 12 PE32+, five signed). The
studio wrote in two languages; the UnityScript is the smaller part and the
older by its stamps. `UnityEngine.dll.mdb` (413,678 B) is a Mono symbol file
for the *engine* assembly — there is none for the game's — filed by its
magic and version (50.0) and not read further.

## The tables

`clrmeta.py` reads a CLR assembly's two string heaps: `#Strings`, 268,112
bytes, 18,316 names; `#US`, 141,056 bytes, 3,015 UTF-16 literals. A heap is a
bag. The pre-briefing said the box "does not read the metadata tables"; it
read three — `cilmeta.py`, from `pc-themurderofsonicthehedgehog-doc`, decodes
Module, TypeRef and TypeDef by ECMA-335 §II.22 and stops at the type names.
Here it was taken further: Field, MethodDef, Param, InterfaceImpl and
MemberRef rows, each table's stride from the heap-size flags and the row
counts, each list cut by the next TypeDef's `FieldList`/`MethodList`, and
seven checks added to the six it had (the lists are monotone and end at
rows + 1; every row ends inside `#~`; every row resolves to a name). All
fifteen pass on `Assembly-CSharp.dll`:

    tables present 29    rows 48,025
    TypeDef 2,124   Field 11,076   MethodDef 11,433   Param 4,959
    MemberRef 3,560   TypeRef 475   CustomAttribute 3,128   Property 1,847
    StandAloneSig 1,521   Constant 1,804   InterfaceImpl 618   NestedClass 472

Forty-two namespaces. The game's own types are the 1,510 in the global
namespace; the rest are libraries compiled in: `InControl` 245 +
`InControl.NativeProfile` 120 (controllers), `SRDebugger.*` 101 + `SRF.*` 51 (a
debug console, whose prefabs are the `srdebugger/*` entries of the
`ResourceManager`), `Tobii.*` 55 (eye tracking), `XInputDotNetPure` 9,
`SimpleJSON` 7, and the InControl *examples* — `MultiplayerBasicExample`,
`MultiplayerWithBindingsExample`, `BindingsExample`, `TouchExample` — shipped
with the library.

## What a Monster is, as a table of types

The type list reads like the game's design document, and the member lists
say what each class holds (`notes/cilmeta-members.txt`):

* **`LevelGeneration`** — 90 fields, 43 methods: `levelSeed`, **`monsterSeed`**,
  `selectedMonster`, `chosenMonstType`, `allMonsters`, `monsterSpawnPoints`,
  `audioLibraries`; `roomPrefabs`, `startRooms`, `victoryRooms`,
  `corridorPrefabs` (`corridor4Ways`, `corridorCorners`, `corridorTJunctions`
  …), `stairPrefabs`, `ventPrefabs`, `doorPrefabs`, `engineRoomPrefabs`,
  `cargoRoomPrefabs`, `deckPrefabs`, `outerDeckWalkways`; `removeSubRoom`,
  `removeEngines`, `removeSea`, `winterWonderland`; `SpawnInitialRooms`,
  `SpawnRandomRooms`, `SpawnCorridors`, `SpawnJointsAndDoors`, `get_MonsterSeed`,
  `get_GetMonster`. The ship is built from prefabs by a seed, and the monster
  is chosen in the same class — which is what the log's `Test Seed` and `Inc:`
  lines are the trace of ([07](07-the-diary-the-crash-and-gog.md)).
* **`MonsterTypeEnum`** — an enum of three: `Brute`, `Hunter`, `Fiend`, in that
  order. **`Monster`** has `MonsterType` and `monsterType`; `ChooseAttack` has
  `whichMonster`; `GlobalMusic` and `EscapeMovieHandler` each carry a
  `monsterType` — the music and the ending film are picked by it
  ([05](05-the-audio-and-the-movies.md)).
* **`MonsterEffectiveness`** — `EffectTotal`, `effectBase`, `EffectMax`,
  `escapeMod`, `increaseItems`, `IncreaseMonsterEffectiveness`,
  `set_HighestEscapeCompleteness`: a difficulty that grows with the escape's
  progress. The log's `Brute k / Hunter k / Fiend k` are **not** this — they
  are a tally of runs per monster, and [07](07-the-diary-the-crash-and-gog.md)
  measures it.
* the monster's mind: `FSM`, `FSMState`, `FSMTransition`, `MonsterStarter`,
  `MRoomSearch`, `MAlertMeters`, `MChasingState`, `MAttackingState2`,
  `MClimbingState`, `MDestroyState`, `ChooseAttack`, `AttackDetection`,
  `BodyTurn`, `DraggedOutHiding`, `RipOffDoor`, `RipOffCurtain`; the three by
  name: `HunterAnimationsScript`, `HunterThreshold`, `FiendLightDisruptor`,
  `FiendDoorSlam`, `MFiendSubDoors`, `Mec_OnFiendRipDoor`, `SteamStunMonster`;
* the escapes: `EscapeChecker` (with a nested `Completeness`), `Helicopter`,
  `DestroyHelicopterEscape`, `RaftEscapeCheck`, `Sub`, `EscapeMovieHandler`;
* and 17 types with `Steam` or `Achiev` in the name, of which `Achievements`,
  `Achieve`, `BarricadeDoorAchievement`, `MonsterKillAchievement` and
  `SteamManager` are Steam's — and `SteamVent`, `DualSteamVent`, `SteamHandle`,
  `SteamPushBack`, `SteamVariation`, `SteamVentManager`, `SteamVents`,
  `SteamStunMonster` are **vapour**: the ship's steam pipes, the
  `MAIN_STEAM_VALVE` achievement's.

## The co-op, measured

The owner remembers a co-op. The object was asked at five levels, each with
its count in both directions:

| level | population | matches | what matched |
|---|---:|---:|---|
| string literals (`#US`) | 3,015 | **0** | nothing for `coop`, `co-op`, `multiplayer`, `lobby`, `network`, `online` |
| types of the game (`TypeDef`, global namespace) | 1,510 | **0** | the 5 `Multiplayer*` types are InControl's examples; `RemoteAntiCulling`, the `*Remote*` profiles and `Tobii*Host*` are TV remotes, Oculus controllers and Tobii |
| fields and methods (`Field` + `MethodDef`) | 22,509 | **0** | `coop\|co_?op\|multiplayer\|lobby\|matchmak\|p2p\|isHost\|isServer\|isClient` |
| imported types (`TypeRef`) | 475 | 0 of networking; 9 of Steamworks | `SteamUserStats`, `SteamAPI`, `SteamClient`, `Callback\`1`, `UserStatsReceived_t`, `GameOverlayActivated_t`, `SteamAPIWarningMessageHook_t`, `Packsize`, `DllCheck`; **no** `SteamMatchmaking`, `SteamNetworking`, `SteamFriends`; 0 rows in `UnityEngine.Networking` (4 in `UnityEngine.VR`) |
| imported members (`MemberRef`) | 3,560 | **0** for `lobby\|matchmak\|network\|p2p\|socket\|multiplayer\|coop`; 12 into Steamworks | `RequestCurrentStats`, `GetAchievement`, `SetAchievement`, `ClearAchievement`, `StoreStats`, `Init`, `Shutdown`, `RunCallbacks`, `SetWarningMessageHook`, `Packsize::Test`, `DllCheck::Test`, a callback constructor |

The nuance that has to be said with it: `Assembly-CSharp-firstpass.dll` is
Steamworks.NET, the whole API, and it *does* contain 23 lobby and
matchmaking types with 214 members (`ISteamMatchmaking*`, `LobbyEnter_t`,
`LobbyInvite_t`, `GameLobbyJoinRequested_t` …) — a library ships everything
it wraps. The game references none of them: its 475 imported types and 3,560
imported members reach Steamworks for achievements and initialisation and
nothing else, and `UnityEngine.Networking.dll` ships beside it with not one
type imported. What would have proved the memory right — a `NetworkManager`
MonoBehaviour, a `SteamMatchmaking::CreateLobby` member, a `Lobby` string —
is absent at every level that could hold it. **This build has no co-op in its
words, its types, its members or its imports.** Whether another edition has
one is not the object's to say and was not looked up.

## The literals, sorted

3,015, dumped with `PYTHONIOENCODING` unset after the repair of
[08](08-tools-predictions-and-calibration.md). Among them: the sixteen
achievement keys the log saves (`ESCAPE_HELICOPTER`, `ESCAPE_LIFERAFT`,
`ESCAPE_SUBMARINE`, `ESCAPE_BRUTE`, `ESCAPE_HUNTER`, `ESCAPE_FIEND`,
`FIRE_EXTINGUISH`, `ITEM_HUNTER_TRAP`, `MAIN_STEAM_VALVE`, `FLAREGUN_BRUTE`,
`POWER_FUSEBOXES`, `BARRICADE_ENTER`, `MONSTER_KILLS`, `MONSTER_TRAP`,
`MONSTER_PITFALL_KILL`, `GLOWSTICKS`); `Loading Achievements from Steam` and
`Loading Achievements from Local`; `'Brute'`, `'Hunter'`, `'Fiend'` as the
sixth, seventh and eighth literals; 144 `Noises/…` and 10 `Music/…` keys;
`Test Seed: `, `Count data:`, `Inc: `, `Saved: ` — the log's own vocabulary;
InControl's controller names (`©Microsoft Corporation Xbox Original Wired
Controller`, `SHENGHIC 2009/0708ZXW-V1Inc. PLAYSTATION(R)3Conteroller`), the
only `©` and the only years (2009) in the heap — no copyright line of the
studio's, no release year, no version string of the game's own.

## What is not measured

* the IL: 11,433 method bodies exist and none was read — reading IL is not
  owed, and how `monsterSeed` becomes `selectedMonster` is a method's;
* the signatures: fields are named, not typed; parameters are counted (4,959),
  not listed;
* the `Constant` table (1,804 rows), which would give `MonsterTypeEnum`'s
  values — assumed 0, 1, 2 by order and not read;
* the `CustomAttribute` rows (3,128) and the `Assembly`/`AssemblyRef` rows
  (which of the seven referenced assemblies is which);
* the firstpass and UnityScript assemblies beyond their censuses.
