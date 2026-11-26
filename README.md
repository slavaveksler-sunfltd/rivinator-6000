# RIVINATOR 6000

<img src="logo.png" alt="RIVINATOR 6000 mascot" width="320">

A browser tool for inspecting Rive `.riv` files in **@rive-app/canvas 2.37.6**, the web runtime our games ship. Open the page, drop a `.riv` on it, and play it the way the game does.

Files never leave your browser: nothing is uploaded anywhere.

## What it does

- **Data:** the artboard's view model as one tree.
  - The main view model's triggers fired from code (no data bind in the file writes them) sit on top as large green buttons, `activate` first. Nested view models keep theirs in the tree and on their cards.
  - The main view model (its name contains "main") comes next, in larger rows: its triggers and values set from code are cards in two columns, tinted with their type color, ordered trigger, number, enum, boolean, string (click a trigger card to fire it, a boolean card to toggle it). Then every nested view model.
  - Inside each view model, values set from code come first, marked with a green **runtime** tag; values Rive sets follow, then events.
  - Legacy state machine inputs (triggers, numbers, booleans), if the state machine has any, are listed under the main view model on grey cards: fire the triggers, edit the numbers and booleans. Fired inputs show in the trigger log. Under them, the artboard's exported text runs (named in Rive, set from code with `setTextRunValue`) are listed with their current text, editable in place. Files without a view model show their inputs and text runs too.
  - Nested view models start collapsed; the main one is open.
  - Every value is editable and every trigger can be fired.
  - Press **Space** to fire `activate` (unless you're typing in a field).
- **Colors by type:** view model brown, number blue, string red, boolean yellow, enum purple, trigger green (fired from code), event cyan (fired by Rive, through actions or property groups bound object to source), qa_ triggers orange.
- **How origins are read:** the inspector reads the data binds stored in the `.riv` itself. A property that a bind writes (target to source, or two-way) is set by Rive; everything else is set from code. If the file later fires or changes something it classified as set from code, it moves it over.
  - Besides data binds, state and transition "fire trigger" actions count as Rive firing a trigger, and name-based binds (property groups bound by name) are resolved through the file's name table.
  - Writes from a state machine layer named **DEMO** don't count: a trigger or value only a DEMO layer writes stays a runtime one (its fires show as `demo` in the log). Anything also written from another layer or a listener is still set by Rive.
  - `activate` always leads the buttons on top, wherever it is and whatever sets it.
- **Player:** the artboard running in 2.37.6, sized for portrait (mobile) artboards. Pick the artboard, state machine, view model instance and fit, and play at 0.25× to 4× speed.
- **Texts:** view models whose name has "bind" or "text" (localization strings) get a compact window above Nested objects: `Language` as a card on the left, every other string as a short editable line.
- **Nested objects:** a card for every nested view model that is bound to an artboard (a component such as a cup or a prize). Each card shows:
  - that artboard as a live thumbnail
  - its triggers, one click to fire
  - its values, editable in place (each name in its type's color; a green dot marks values set from code, except a number named `index`, which is shown as an internal value)

  Triggers and values set from code come first, together in a green-tinted block and in the main view model's order (triggers, numbers, enums, booleans, strings); what Rive sets itself follows.

  Cards are grouped by their view model (CupVM, PrizeVM…), with its name and count above each group. List view puts each object on one compact line: a small thumbnail, its name, its triggers and its values (scroll the line sideways if it runs long). Grid view shows tiles with the thumbnail on the left and the name, triggers and values to its right. Values set from code get compact versions of the Data panel's controls: a switch for booleans and `‹ 5 ›` arrows for numbers (Shift-click steps by 10). A card flashes when a trigger inside it fires, and the card with the latest trigger moves to the top of its group.
- **Trigger activations:** every view model trigger the file fires, with time and frame, subscribed with `.on()` the way the game integration listens.
- **qa_ screenshots:** each `qa_` trigger is screenshotted the frame it fires. Click its row in the log to see it, step through all of them, and save any as PNG.
- **Performance:** a compact readout under Data. It shows the runtime's time per frame for the main artboard (average and worst), fps, dropped frames, card thumbnails' cost, and memory, with a short graph. The inspector only reads the clock around the runtime's draw; nothing in the file or runtime changes. Compare files or moments on the same machine: a phone is several times slower than a desktop, so green means under a quarter of the frame budget.
- **Assets:** the file's embedded images, fonts and sounds, next to the trigger log. List view (small thumbnail on the left) or grid view (bigger thumbnails), sorted by name, size or type. Click an image's thumbnail to see it full size in a popup (a font shows a type specimen); click the thumbnail again, move the cursor away or press Esc to close it. PNG images are tinted red and always listed first, with a warning icon by the Assets title: they are usually far bigger than WebP, so switching them to WebP in Rive (Compression › Format) is the quickest size win. A tag by each name shows the format the asset is really stored in (WEBP, PNG, JPG, MP3, WAV, TTF…), read from its bytes rather than the extension it was imported with. WebP images also show their export quality in the same tag (`WEBP 75%`), the "Quality (Lossy)" value set in Rive. The .riv doesn't store that setting, so it is read back from the WebP's quantizers (libwebp's quality formula, checked against Rive exports). Neighbouring values such as 74 and 75 can produce the same file; when an image fits several, the value most of the file's images use is shown, marked `~` if it is still uncertain. Lossless WebP shows "lossless". Replace any asset live, with the Replace button or by dropping a file on it: the player swaps it at once, Restart keeps it, Reset puts the original back. The .riv file itself is never changed.
- **File size breakdown:** above the assets, a bar showing what the .riv's bytes are: images, fonts, sounds, keyframes, state machines, data binding, shapes and paints, text, bones and constraints, layout, scripts and other. Every object in the file is counted under its type's group, so the numbers add up to the file size exactly. Click `+N` to see every group with its size.
- **Recording:** record the artboard (or the whole tab) to MP4, with the sound the file plays.

Panels are resizable: drag the bars between them; double-click a bar to reset it.

**Winter theme:** falling snow, Santa hats on the panels and the mascot, and string lights along the top. It's decoration only: it draws on a layer above the page that takes no clicks and doesn't touch the file, the runtime or its frame timing. It hides while you record the tab. Turn it off with the snowflake button in the top bar; the page remembers your choice.

## Running it locally

The page needs to be served over http (browsers do not load `rive.wasm` from a `file://` page):

```bash
python3 -m http.server 8000
```

Then open http://localhost:8000.

## Files

- `index.html`: the whole tool (HTML, CSS and JavaScript in one file). The runtime script loads from unpkg, pinned to `@rive-app/canvas@2.37.6`.
- `rive.wasm`: the matching 2.37.6 WebAssembly runtime, served next to the page.

To move to another runtime version, change the version in the `<script src>` URL and replace `rive.wasm` with the same version's file from `https://unpkg.com/@rive-app/canvas@<version>/rive.wasm`.
