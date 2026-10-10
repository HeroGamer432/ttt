# Hollow Knight x Bopl Battle – Mashup Design Brief (v0.1)

For the coding agent on the creator's PC. Use together with the Melty publish prompt. The creator's design answers are final; everything marked `TODO(verify)` must be filled in from the real game files, never guessed.

## 1. Decisions (from the creator)

| Topic | Decision |
|---|---|
| Host game (role `primary`) | Hollow Knight (`hollow-knight`), Melty installs the Hollow Knight Modding API (`hk-modding-api`) |
| Companion (role `companion`) | Bopl Battle (custom- slug from `search_games`). Its real content is read from the player's installed folder via `{game:<slug>}`. Never ship Bopl files, never use look-alikes, never label it `secondary` |
| Players | Solo. Mode 4 is local 2-player on one PC. No online multiplayer, so no `recipe.multiplayer.connect` |
| First minute | Own main menu with the four modes. One click, then the player is directly in that mode |
| First release | Menu + Mode 1 done really well. Modes 2–4 show in the menu as "coming soon" (not selectable) |
| Install paths | `C:\Program Files (x86)\Steam\steamapps\common\Hollow Knight` and `...\Bopl Battle` (verify) |

The four modes:
1. **Bopl powers in Hallownest** – the Knight picks up Bopl Battle abilities and uses them against Hollow Knight bosses. (v1)
2. **Boss arena brawler** – Bopl-style arena inside Hollow Knight, Hollow Knight bosses/characters as opponents. (later)
3. **Bopl blobs invade Hallownest** – Bopl blobs as enemies and a boss, with Bopl physics. (later)
4. **Knight vs Knight** – local versus for two players with Bopl arena rules and abilities. (later)

## 2. Order of work for the agent

1. Connect to Melty, `list_my_mods`, `search_mashups` and `game_info` for both games, `mashup_info` on the closest launchable mashup. Tell the creator only if something close exists.
2. Inspect both installs (engine and version, Managed/ folder, Bopl asset format). Both are expected to be Unity; verify.
3. Fill every `TODO(verify)` cell in the sheets below, then run the preflight (section 5).
4. Build the mod on the Hollow Knight Modding API, test in the running game, capture a real screenshot of Mode 1.
5. Package, `inspect_package`, `validate_recipe`, `one_click_check`, create the draft, upload, submit, screenshot, then ask the creator whether to publish.

## 3. Sheets (source of truth – change the sheet before the code)

### modes.json
```json
[
  {"id":"bopl_powers","menuLabel":"Bopl Powers","order":1,"status":"playable","host":"hollow-knight","players":1,"entryScene":"TODO(verify)","description":"The Knight uses Bopl Battle abilities against Hallownest's bosses."},
  {"id":"boss_arena","menuLabel":"Boss Arena","order":2,"status":"coming_soon","host":"hollow-knight","players":1,"entryScene":null,"description":null},
  {"id":"blob_invasion","menuLabel":"Blob Invasion","order":3,"status":"coming_soon","host":"hollow-knight","players":1,"entryScene":null,"description":null},
  {"id":"knight_vs_knight","menuLabel":"Knight vs Knight","order":4,"status":"coming_soon","host":"hollow-knight","players":2,"entryScene":null,"description":null}
]
```

### menu.json (hooks into the game's main menu)
```json
[
  {"id":"mode_select_screen","hookInto":"TODO(verify): HK main menu scene/manager","trigger":"after main menu loads","shows":"modes.json rows ordered by `order`","inputs":["keyboard","controller"],"onSelectPlayable":"load modes[].entryScene","onSelectComingSoon":"disabled entry with 'coming soon' label"}
]
```

### abilities.json (Mode 1; at least 4 rows for v1)
Each row = one Bopl ability the Knight can use. Name, icon, sound and effect are read from the Bopl install, not copied.
```json
[
  {"id":"ability_1","boplAbility":"TODO(verify)","boplAssetPath":"TODO(verify)","icon":"TODO(verify)","sound":"TODO(verify)","effectOnBosses":"TODO(verify)","damage":"TODO(verify)","cooldownSec":"TODO(verify)","soulCost":"TODO(verify)","inputKey":"TODO(verify)","inputPad":"TODO(verify)","dropFrom":"pickups.json"},
  {"id":"ability_2","boplAbility":"TODO(verify)","boplAssetPath":"TODO(verify)","icon":"TODO(verify)","sound":"TODO(verify)","effectOnBosses":"TODO(verify)","damage":"TODO(verify)","cooldownSec":"TODO(verify)","soulCost":"TODO(verify)","inputKey":"TODO(verify)","inputPad":"TODO(verify)","dropFrom":"pickups.json"},
  {"id":"ability_3","boplAbility":"TODO(verify)","boplAssetPath":"TODO(verify)","icon":"TODO(verify)","sound":"TODO(verify)","effectOnBosses":"TODO(verify)","damage":"TODO(verify)","cooldownSec":"TODO(verify)","soulCost":"TODO(verify)","inputKey":"TODO(verify)","inputPad":"TODO(verify)","dropFrom":"pickups.json"},
  {"id":"ability_4","boplAbility":"TODO(verify)","boplAssetPath":"TODO(verify)","icon":"TODO(verify)","sound":"TODO(verify)","effectOnBosses":"TODO(verify)","damage":"TODO(verify)","cooldownSec":"TODO(verify)","soulCost":"TODO(verify)","inputKey":"TODO(verify)","inputPad":"TODO(verify)","dropFrom":"pickups.json"}
]
```
Rule: if a Bopl ability cannot be read from the installed files in a usable way, tell the creator and propose the closest alternative before changing this sheet.

### pickups.json
```json
[
  {"id":"pickup_between_rounds","grants":"one random ability from abilities.json","spawn":"between boss fights, in the arena room","visual":"TODO(verify)","pickupSound":"TODO(verify)","replacesPreviousAbility":"TODO(verify)"}
]
```

### bosses.json (at least 3 for v1; candidates are proposals, verify they exist and can be loaded standalone)
```json
[
  {"id":"boss_1","hkBoss":"False Knight (candidate)","scene":"TODO(verify)","hpOverride":"TODO(verify)","order":1,"arenaRoom":"TODO(verify)"},
  {"id":"boss_2","hkBoss":"Hornet (candidate)","scene":"TODO(verify)","hpOverride":"TODO(verify)","order":2,"arenaRoom":"TODO(verify)"},
  {"id":"boss_3","hkBoss":"Mantis Lords (candidate)","scene":"TODO(verify)","hpOverride":"TODO(verify)","order":3,"arenaRoom":"TODO(verify)"}
]
```

### rounds.json (flow of Mode 1)
```json
[
  {"id":"round_1","boss":"boss_1","abilitiesOffered":2,"onWin":"round_2","onDeath":"retry round"},
  {"id":"round_2","boss":"boss_2","abilitiesOffered":2,"onWin":"round_3","onDeath":"retry round"},
  {"id":"round_3","boss":"boss_3","abilitiesOffered":2,"onWin":"victory screen","onDeath":"retry round"}
]
```

### hooks.json (every game system the mod touches)
```json
[
  {"id":"menu","game":"hollow-knight","what":"main menu","how":"TODO(verify)"},
  {"id":"scene_load","game":"hollow-knight","what":"load boss/arena scenes","how":"TODO(verify)"},
  {"id":"player_input","game":"hollow-knight","what":"bind ability keys/buttons","how":"TODO(verify): game_info says how a mod reads key bindings"},
  {"id":"player_damage","game":"hollow-knight","what":"apply ability damage to bosses","how":"TODO(verify)"},
  {"id":"bopl_assets","game":"bopl-battle","what":"read ability art, sound and data from {game:<slug>}","how":"TODO(verify): file format and loading method"}
]
```

## 4. Melty recipe notes

- Hollow Knight: role `primary`, loader `hk-modding-api` requested in the recipe (not bundled). Mod folder: `{game}/hollow_knight_Data/Managed/Mods`.
- Bopl Battle: role `companion`; the mod receives its folder as `{game:<slug>}`. Add `recipe.together` only if Bopl Battle really has to run at the same time (not expected; the mod reads files).
- Bundle only what the license allows, with credit. No Hollow Knight or Bopl Battle files, textures, sounds or models in the upload.
- Not multiplayer: do not set `recipe.multiplayer` (Mode 4 is local, and not in v1 anyway).

## 5. Preflight (before every build)

List and fix before building:
1. Every `TODO(verify)` cell still left in any sheet.
2. Every reference that does not resolve (`dropFrom`, `boss`, `entryScene`, `onWin`).
3. Every ability with no input binding, or with a binding that collides with a Hollow Knight default.
4. Every Bopl asset the mod reads that cannot be found in the installed folder.

## 6. Listing draft (adjust to the actual build)

- Title: **Hollow Knight: Bopl Powers**
- Tagline: Take Bopl Battle's chaos into Hallownest's boss fights.
- Description: Pick a mode from the new main menu and fight Hollow Knight's bosses with abilities from Bopl Battle. The first release has Bopl Powers mode; Boss Arena, Blob Invasion and a local two-player Knight vs Knight mode are coming. Needs Hollow Knight and Bopl Battle on Steam.
- Credits, content license and remix permission: **ask the creator**, do not guess.

## 7. Test and media

- Test: start from the Melty Play button, open the menu, pick Bopl Powers, use each ability once, beat all three bosses, die and retry once.
- Media: a real screenshot of Bopl Powers running, showing an ability hitting a boss. Game window only, no private windows.
