# Icon picker for Herdr

A Herdr mod: a dialog for choosing an icon, from every Symbols Nerd Font glyph (over 10,000), every emoji (over 1,800), or the first letter of a name. It has a Suggested tab, Glyphs and Emoji tabs with a chip per glyph set or emoji group, and one search box that looks through all of them by name and keyword. Arrow keys move over the grid, Enter picks, Esc closes.

The picker draws nothing on its own: another mod opens it and decides where the chosen icon shows.

> Screenshot: not included yet.

## Install

Clone it into Herdr's mods folder under the name `icon-picker` (the mod reads its name files from `mods/icon-picker/names`):

```sh
git clone <this repository> ~/.config/herdr-app/mods/icon-picker
```

The mods folder is `$HERDR_APP_HOME/mods` when that is set, else `$XDG_CONFIG_HOME/herdr-app/mods`, else `~/.config/herdr-app/mods`. Herdr picks the mod up as soon as the folder appears. Check it loads with `herdr-app check ~/.config/herdr-app/mods/icon-picker`.

Glyphs need the Symbols Nerd Font (family name `Symbols Nerd Font`) installed; without it glyph tiles show as missing characters, while emoji and letters still work.

## Opening it from another mod

Open it as a dialog and give it a shared key and an id:

```js
import { openOverlay, shared } from "herdr";

openOverlay({ mod: "icon-picker", kind: "dialog", width: 452, height: 392, props: { shared: "my-mod.icons", id: space.id, name: space.name } });

// Later, when drawing:
const icon = (shared("my-mod.icons") ?? {})[space.id]; // { kind: "glyph" | "emoji", value } or { kind: "letter" }, undefined when automatic
```

| Prop | Required | Meaning |
| --- | --- | --- |
| `shared` | yes | The `shared()` key holding a map of id to icon. The picker writes `shared(key)[id] = { kind, value? }`; Clear icon deletes the entry. |
| `id` | yes | Which entry in that map. |
| `name` | yes | Shown in the title ("Icon for …") and used for the Letter tile. |
| `letter` | no | The letter to offer instead of the first letter or digit of `name`. |
| `now` | no | The icon shown today, `{ kind, text }`, drawn in the footer. |
| `extra` | no | Tiles of your own kinds beside the letter, `[{ kind, text, word }]` (`text` is a glyph, `word` its label). Picking one writes `{ kind }`. |

Draw a glyph with `.font_family("Symbols Nerd Font")`; an emoji or a letter as plain text.

### Panes and tabs

With props `{ pane }` or `{ tab_id }` the picker keeps the choice on that pane or tab through the `pane.set_icon` / `tab.set_icon` commands. Stock Herdr has neither command, so the picker checks `commands()` when it opens and, where they are missing, says "This Herdr can't keep icons on panes or tabs." instead of offering a choice that goes nowhere.

### Space icons

Stock Herdr's spaces sidebar draws each space's avatar as a coloured tile with the first letter of the space's name; it reads only a colour (`shared("space.colors")`) and no icon, so a mod cannot put a picked icon there. Showing space icons needs either a sidebar that reads one (for example from a `shared()` map this picker writes) or a Herdr change; this mod does not include one.

## Tests

```sh
bun test
```

The tests load `main.js` without a window, against a recorded stand-in for Herdr's UI and `herdr` modules, and check the catalogue, search ranking, the grid, picking and the pane/tab gate.

## Credits and licences

- Glyph names: Nerd Fonts (https://github.com/ryanoasis/nerd-fonts), MIT.
- Emoji names, keywords and groups: emojibase (https://github.com/milesj/emojibase), MIT; its names and keywords come from Unicode CLDR, under the Unicode License v3.
- Full notices: `names/LICENSES.md`, `names/LICENSE-NerdFonts.txt`, `names/LICENSE-emojibase.txt`.
- The mod itself: MIT, see `LICENSE`.
