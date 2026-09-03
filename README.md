# MK-Smart Home Lab

An isometric floor-plan editor that doubles as a smart-lighting scene simulator and a **Home Assistant** config generator.

Draw your home by dragging room blocks onto a canvas, fit each room with lights and covers, click a lamp to watch the room respond — then export the whole thing as a Home Assistant dashboard.

**[▶ Live demo](https://ming-kun.github.io/mk-ha-floorplan-editor/)** · One HTML file · No build step · No runtime dependencies

---

## Why

Getting a floor plan into Home Assistant usually means opening a vector editor, drawing the rooms by hand, exporting a PNG, then hand-writing the YAML that pins each entity to the right pixel coordinate — and doing it again every time a room changes.

This does all of that in one page. The layout you draw *is* the entity registry: rooms become areas, the devices you drop into them become entities, and the export writes the coordinates for you.

## Features

**Drawing**
- Drag room blocks onto the canvas; rooms auto-space by two cells so none occludes the one behind it
- Multiple floors — add, reorder, delete; names renumber by position
- 2:1 isometric projection, rendered as a runtime-assembled SVG string (no canvas, no WebGL)

**Devices** — five placeable entity types per room:

| Type | Home Assistant domain |
|---|---|
| Smart ceiling light | `light` · brightness + color_temp |
| Dimmer switch | `light` · brightness |
| Smart switch | `switch` |
| Smart curtain | `cover` · curtain · position |
| Smart roller blind | `cover` · shade · position |

**Scene simulation** — an 8-state matrix of day/night × lights on/off × covers open/closed, with brightness and cover position on continuous sliders. Press <kbd>D</kbd> to flip day/night. Click any lamp or switch on the plan to toggle it directly.

**Export** — one dialog, four artifacts plus the background image:

- `picture-elements` YAML — the **native** HA card, no custom components required
- `ha-floorplan` config + `home.css` — for the [ha-floorplan](https://github.com/ExperienceLovelace/ha-floorplan) custom component
- `scene.json` — the raw scene model
- Background PNG at the exact projection the coordinates assume

**12 languages** — English, 中文, Français, Deutsch, Italiano, Español, Português, Nederlands, Polski, Русский, 한국어, 日本語.

## Use it

Open the [live demo](https://ming-kun.github.io/mk-ha-floorplan-editor/), or run it locally:

```bash
git clone https://github.com/Ming-kun/mk-ha-floorplan-editor.git
open mk-ha-floorplan-editor/index.html
```

There is nothing to install and nothing to build. `index.html` is the entire application — open it from disk and it works offline, apart from the webfont.

### Getting it into Home Assistant

1. Build your plan, set the scene state you want as the dashboard's resting look
2. **Export to Home Assistant** → **Background** tab → save the PNG to `config/www/floorplan/home.png` (served at `/local/floorplan/home.png`)
3. **picture-elements** tab → copy the YAML
4. In HA: create a dashboard, switch to YAML mode, paste

The exported entity ids follow `<domain>.<area>_<device>` — for example `light.living_room_ceiling`. Rename them to match your real entities, or name your HA entities to match and skip the step.

## How it works

Everything on screen — walls, furniture, fixtures, covers — is the same box primitive, shaded across three faces. A lighting state therefore changes a handful of coefficients rather than re-deriving geometry, which is why the whole scene stays visually consistent across all eight states instead of drifting per-object.

The data model is deliberately isomorphic to Home Assistant's own `floor` → `area` → `entity` registry. Export is a projection of that model, not a translation layer, so adding an entity type means adding it in one place.

## Compatibility

Any current browser. Uses `localStorage` for the language preference, wrapped in `try`/`catch`, so private-browsing mode degrades to the default rather than breaking.

## License

[Apache License 2.0](LICENSE) — commercial use, modification and redistribution permitted; see the license for the patent grant and attribution requirements.
