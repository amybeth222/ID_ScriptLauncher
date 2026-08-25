# Script Launcher (InDesign CEP panel)

Same panel as the Illustrator version, with full feature parity — a dockable
panel that scans folders you choose and lists every `.jsx`/`.jsxbin` script
in them as a clickable RUN button. Only real difference from the Illustrator
build: the manifest targets InDesign (`Host Name="IDSN"`) instead of
Illustrator (`ILST`).

## What's in here

```
IDScriptLauncher/
  CSXS/manifest.xml       - extension manifest (host: InDesign, CEP 9)
  client/index.html       - panel UI
  client/style.css        - dark theme, customizable UI color accent
  client/main.js          - panel logic (scan / filter / run / persist folders)
  client/lib/CSInterface.js - minimal JS<->host bridge
  client/icons/           - panel icon (docked-panel badge, light/dark/@2x)
  host/main.jsx            - ExtendScript: list files, run a script, folder picker
```

## Install (unsigned / dev mode)

You've already got `PlayerDebugMode` enabled for CSXS 9 from the Illustrator
panel, so no need to redo that step.

**Mac:**
1. Copy the whole `IDScriptLauncher` folder into:
   ```
   ~/Library/Application Support/Adobe/CEP/extensions/
   ```
   (same folder the Illustrator `ScriptLauncher` folder lives in — CEP
   extensions are shared across Adobe apps, InDesign just only loads the
   ones whose manifest lists it as a compatible host.)
2. Restart InDesign.
3. Open it via **Window > Utilities > Extensions > Script Launcher**
   (InDesign nests extensions under Utilities, unlike Illustrator's
   Window > Extensions). Note: the Extensions menu item only appears once
   at least one compatible extension is installed — that's normal CEP
   behavior, not a sign anything's broken.

**Windows:** same idea — copy into
`C:\Program Files (x86)\Common Files\Adobe\CEP\extensions\`, restart
InDesign.

## Where to point it

InDesign's own Scripts panel looks in:
```
~/Library/Preferences/Adobe InDesign/[version]/en_US/Scripts/Scripts Panel
```
You can point this launcher at that same folder if you want parity with
InDesign's native Scripts panel, or at any other folder — like wherever your
Script Depository `.jsx` files live locally.

## Using it

- First launch defaults to your Desktop. Open ⚙ **Settings** and use
  **+ Add Folder** under "Folder Sources" to point it at wherever you keep
  your `.jsx` files.
- You can add multiple folder sources. With just one folder, its scripts
  show in a flat list. Once you add a second (or more), each source's
  scripts — including ones sitting directly in its root — collapse into
  their own named toggle group, so scripts from different sources don't get
  mixed together. Remove a source anytime with the × next to it.
- Folder choices are remembered (stored in the panel's localStorage) so it
  reopens on the same folders next time.
- Scanning is recursive — subfolders show up as collapsible groups.
- Click anywhere on a script row (or the RUN button) to execute it via
  `$.evalFile()`.
- Drag the grip icon (⣿) on the left of a row or folder group to reorder the
  list. The order is remembered per folder selection.
- ⟳ rescans the current folders if you've added/removed scripts.
- The search box filters the visible list by filename.
- ⚙ opens display settings: text size/color, **UI Color** (drives the RUN
  button, hover/focus borders, and every accent stroke in the panel), row
  spacing, and Folder Sources.
- ⓘ opens an About popup with the current version number and author/site
  info.

## Packaging as a `.zxp` for distribution

Same process as the Illustrator panel — reuse the same signing certificate:

```
find IDScriptLauncher -iname ".DS_Store" -delete
ZXPSignCmd -sign IDScriptLauncher IDScriptLauncher.zxp cert.p12 yourPassword
```
(run from the parent folder that contains `IDScriptLauncher/`. The
`.DS_Store` cleanup matters: Adobe Exchange's upload scanner rejects
packages containing macOS's hidden Finder metadata files.)

If submitting to Adobe Exchange, watch for the same branding-guideline gotcha
hit on the Illustrator listing: the plugin name can't lead with the host app
name ("InDesign Script Launcher" would likely get flagged the same way
"Illustrator Script Launcher" did) — use "Script Launcher for InDesign"
instead, and keep "InDesign" capitalized in tags.

## Notes / easy extensions

- `.jsxbin` files are listed too, since `$.evalFile()` runs those fine.
- Swap `client/lib/CSInterface.js` for Adobe's official CSInterface.js from
  the CEP-Resources repo if you want the fuller API — this build only wires
  up what the launcher needs.
- See the Illustrator panel's README for more background on the packaging
  workflow and known gotchas, since both panels share the same client code.
