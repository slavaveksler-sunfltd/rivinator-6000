# RIVINATOR 6000

<img src="logo.png" alt="RIVINATOR 6000 mascot" width="320">

A browser tool for inspecting Rive `.riv` files in **@rive-app/canvas 2.37.6**, the web runtime our games ship. Open the page, drop a `.riv` on it, and play it the way the game does.

Files never leave your browser: nothing is uploaded anywhere.

## What it does

- **Data:** the artboard's view model as one tree, with triggers, values and nested view models in the order the file stores them. `activate` and the main view model's number values stay pinned at the top. Every value is editable and every trigger can be fired.
- **Player:** the artboard running in 2.37.6, sized for portrait (mobile) artboards. Pick the artboard, state machine, view model instance and fit, and play at 0.25× to 4× speed.
- **Nested objects:** a card for every nested view model that is bound to an artboard (a component such as a cup or a prize). Each card shows:
  - that artboard as a live thumbnail
  - its triggers, one click to fire
  - its strings, numbers and enums, editable in place

  A card flashes when a trigger inside it fires.
- **Trigger activations:** every view model trigger the file fires, with time and frame, subscribed with `.on()` the way the game integration listens.
- **qa_ screenshots:** each `qa_` trigger is screenshotted the frame it fires. Click its row in the log to see it, step through all of them, and save any as PNG.
- **Recording:** record the artboard (or the whole tab) to MP4, with the sound the file plays.

Panels are resizable: drag the bars between them; double-click a bar to reset it.

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
