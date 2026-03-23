# Halo Wars DE — Scenario Editor

A browser-based scenario editor for **Halo Wars: Definitive Edition**, built as a single HTML file with no build step, no npm, no dependencies to install.

![Version](https://img.shields.io/badge/version-v0.1.1-blue)
![License](https://img.shields.io/badge/license-MIT-green)

---

## Features

- Load and parse `.scn` scenario files
- Place, move, and delete objects (bases, hooks, creep markers, supply crates, and more)
- **Prefab camps** — drop a complete creep camp (difficulty + reward + unit markers + activation sphere) in one drag
- **Design Sphere support** — creep camp activation zones placed automatically with prefabs, visible as dashed circles on the canvas
- Object classification with colour-coded layers (Gameplay, Creep/AI, Supply, Units, Art)
- Accurate Y-coordinate placement via inverse-distance-weighted interpolation from existing terrain objects — no heightmap required
- Inspect and edit object properties (name, position, player assignment)
- Full undo/redo history, copy, paste, delete
- Export modified `.scn` files ready to drop into your mod folder
- Searchable object catalogue with collapsible sections
- Layer visibility toggles
- Custom icon support per object type

---

## Usage

1. Download `scenario_editor.html`
2. Open it in any modern browser — no server required
3. Load your `.scn` file using the **SCN FILE** button
4. Edit, place, and move objects on the canvas
5. Export with **EXPORT SCN** when done

---

## How Creep Camps Work

This took a while to figure out, so it's documented here for other modders.

Creep camps in Halo Wars DE are driven by `creeps.triggerscript`, which runs for every skirmish map. The system works like this:

1. **Design Spheres** in `<DesignObjects><Spheres>` with `<Type>Creeps</Type>` define activation zones. The skirmish engine finds all spheres of this type at load time.
2. For each sphere, the creep script scans for all marker objects (`sys_rebelMarker_*`, `sys_creepDifficulty_*`, `sys_creepReward_*`, etc.) within the sphere's radius.
3. Those markers determine which units spawn and how the camp behaves — difficulty sets veterancy, reward determines loot drops on kill.

**Key rules:**
- Placing marker objects alone does nothing — each camp needs a Design Sphere covering it
- All markers within a sphere's radius are claimed by that camp
- Don't overlap spheres or markers may be double-counted
- The prefabs in this editor handle all of this automatically in one drag

### Adding new camps

Use the prefabs in the **PREFABS — REBEL** or **PREFABS — FORERUNNER** catalogue sections. Each prefab places the full set of markers plus a Design Sphere.

To add more units to an existing camp, drop individual markers inside its sphere (shown as a dashed white circle on the canvas).

### Prefab reference

| Prefab | Contents |
|---|---|
| Rebel Camp (Easy) | difficulty easy + reward small + 2 infantry |
| Rebel Camp (Medium) | difficulty medium + reward medium + 2 infantry + 1 sniper |
| Rebel Camp (Hard) | difficulty hard + reward large + 2 infantry + 1 sniper + 1 turret |
| Forerunner Outpost (Easy) | difficulty easy + reward small + 2 sentinels |
| Forerunner Outpost (Hard) | difficulty hard + reward large + 2 sentinels + 1 super sentinel |

---

## Mod Pipeline

Place your exported `.scn` at:
```
[ModFolder]/ModData/scenario/skirmish/design/[mapname]/[mapname].scn
```

Point `ModManifest.txt` at your mod folder:
```
%localappdata%\Halo Wars\ModManifest.txt
```

The game reads plain XML `.scn` files directly — no XMB conversion needed. Other map files (`.sc2`, `.sc3`, `.gls`, `.fls`) do not need to be modified for basic object placement.

---

## Keyboard Shortcuts

| Action | Shortcut |
|---|---|
| Undo | Cmd/Ctrl + Z |
| Redo | Cmd/Ctrl + Shift + Z |
| Copy | Cmd/Ctrl + C |
| Paste | Cmd/Ctrl + V |
| Delete | Backspace / Delete |

---

## SCN File Format Notes

The `.scn` is plain XML (ASCII encoded). Key sections:

- `<Objects>` — all placed objects. New objects must go here, not at the top level of `<Scenario>`
- `<DesignObjects><Spheres>` — creep camp activation zones
- `<Positions>` — player start positions
- `<TriggerSystem>` — map-specific scripting (not modified by this editor)

Map bounds are derived from the maximum X/Z values of existing objects. The coordinate origin (0,0) is the top-left corner of the map.

---

## Roadmap

| Version | Goal |
|---|---|
| v0.1.1 | ✅ Current — Y interpolation, classifier fixes, prefab camps with Design Spheres |
| v0.2.0 | Visual pass — distinct shapes per type, forward direction ticks, influence rings |
| v0.3.0 | XTD heightmap parsing — accurate Y snapping + rendered terrain background |
| v0.3.x | XTT terrain texture extraction — top-down map image auto-aligned to coordinate space |
| v0.3.x | Map registration UI (scenariodescriptions.xml generation) |
| v1.0.0 | Stable release |
| v2.0.0 | Triggerscript / win condition editor |

---

## Known Limitations

- Forerunner sentinel markers may not activate on maps without existing forerunner trigger infrastructure in the SCN TriggerSystem
- Map bounds derived from object extents — very sparse maps may have imprecise coordinate scaling
- No terrain background — objects render on a dark grid (planned for v0.3.0)
- TriggerSystem is not modified by the editor — custom objectives and win conditions require manual trigger editing (planned for v2.0.0)

---

## Built With

- [React 18](https://react.dev/) via CDN
- [Babel Standalone](https://babeljs.io/docs/babel-standalone)
- No build step — single `.html` file

---

## Community & Resources

- HW:DE Discord — active modding community
- PhxGUI— XMB↔XML conversion tool

---

## License

MIT © 2025 Tom van Ryswyk ([BoldBoimler](https://github.com/BoldBoimler))

## Acknowledgements
 
Built in collaboration with [Claude](https://claude.ai) (Anthropic). The creep system reverse engineering, Design Sphere discovery, and much of the architecture was worked out through iterative development sessions with Claude.
