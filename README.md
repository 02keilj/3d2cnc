# 3D2CNC

Turn a 3D model into flat parts that slot together, ready to cut on a CNC router or laser cutter.

3D2CNC is a browser-based take on Autodesk's discontinued 123D Make. Load an STL, OBJ or 3MF file, choose how to slice it, and export cut-ready DXF and SVG sheets. It adds the settings a real workshop needs: bit diameter, dogbones, slot clearance, laser kerf and single-stroke engraving labels.

**Live tool:** https://02keilj.github.io/3d2cnc/

Everything runs in your browser, so your models are never uploaded anywhere.

---

## Features

### Construction methods
- **Crate (interlocked slices).** Two families of panels at right angles slot together egg-crate style. You can pick the slice directions (X/Y, X/Z or Y/Z), the panel count for each family and which family drops onto the other.
- **Radial.** Fins radiate around the vertical axis and slide onto stacked horizontal rings. This suits vases, pots and other turned shapes. The hub radius is calculated automatically so neighbouring fins don't collide.
- **Stacked layers.** The model is sliced into layers one stock-thickness apart, to glue together in order.

### Joints that work in material
- Slot width is the stock thickness plus a clearance you set.
- **CNC corner relief:** dogbone, T-bone (deeper), T-bone (wider) or none, sized from your bit diameter.
- **Laser mode** has optional kerf compensation.
- **Edge cases are handled.** If two panels only graze each other, the crossing gets a one-sided notch instead of a weak joint. Joints that would leave a thin sliver are skipped. A slot can still break out of a panel edge as a clean rebate, for example on a panel sitting on the floor.
- **Fragments are cleaned up.** Small pieces under a minimum size are removed, as are pieces with no joints (optional). Joints that pointed at a removed piece are cancelled and the layout is rebuilt until it is stable.

### Keep-out bodies
Include extra bodies in your file, such as a lid that sits in a recess or an insert panel. Give each body a role:

| Role | Effect |
|---|---|
| Slice | Normal geometry that gets sliced |
| Keep-out | Panels are trimmed clear of it through their full thickness, plus a clearance |
| Keep-out + cut part | As above, and the body is also exported as a flat part (K1, K2, …) |
| Ignore | Left out entirely |

A body counts as separate when its surface is closed on its own, so a lid resting in its recess stays separate even where the two share corner points.

### Interactive assembly view
- Orbit the 3D preview, with a transparent model shown behind it.
- **Drag panels to move them.** Crate panels and rings slide along their normal and radial fins rotate around the axis. Joints regenerate when you release.
- Use **To floor** and **To top** (or the −/+ ends of the axis) to place a selected panel flush with the edge of the model.
- **Reset positions** returns every panel to the generated layout.
- The **Lift** slider separates the sliding family so you can check the assembly direction.
- Click a part to highlight it in the 3D view, the sheet layout and the parts list together.

### Output
- One **DXF** (R12, millimetres) and one **SVG** per sheet, plus a **CSV cut list**, downloaded together as a ZIP.
- DXF layers:
  - `CUT_OUTER`: outer profiles; cut outside the line.
  - `CUT_INNER`: holes; cut inside the line.
  - `LABELS`: single-stroke engraving, as open polylines.
  - `SHEET`: optional sheet outline, for reference only.
- SVG colours are red for outer cuts, blue for inner cuts and black for engraving.
- Each part is engraved with its own name. Each slot is labelled with the name of the panel that fits into it.
- A simple shelf layout packs the parts onto sheets of your chosen size. For tighter material use, run the DXFs through a dedicated nester such as Deepnest, or the nesting in your CAM software.

### Other
- Load STL (binary or ASCII), OBJ or 3MF, including 3MF files with components and multiple parts.
- Use the model at its file size or scale it to fit a given width, depth or height.
- Rotate the model in 90° steps.
- Light, dark or automatic theme.
- Sample models are included for quick testing.

---

## Quick start

1. Open the tool and load your model, or start from one of the samples.
2. Set the **stock thickness** and either your **bit diameter** (CNC) or **kerf** (laser).
3. Choose **Crate**, **Radial** or **Stacked**, and the number of panels.
4. Check the **Assembly** view. Drag panels into position if needed, and read any warnings in the bar at the bottom.
5. Check the **Sheets** and **Parts** tabs.
6. Click **Download ZIP** and take the DXF or SVG files into your CAM or laser software.

**Tip:** cut a test piece first to dial in slot clearance. 0.1–0.3 mm suits most plywood and MDF on a router. A laser with kerf compensation usually needs less.

---

## Settings reference

| Setting | What it does |
|---|---|
| Scale | Use the file's own size in mm, or fit the model to a width, depth or height |
| Slice directions | Which two axes the crate panel families are perpendicular to |
| A / B panels | Number of panels in each crate family |
| Assembly direction | Which crate family is lowered onto the other |
| Radial fins / rings | Number of radial fins (R) and horizontal rings (H) |
| Hub radius | Inner radius where fins stop; 0 means the smallest that clears |
| Remove parts with no joints | Drops pieces that wouldn't connect to anything |
| Minimum part size | Drops pieces smaller in area than a square of this width |
| Stock thickness | Board thickness; sets slot width and layer spacing |
| Slot clearance | Extra slot width for an easy fit |
| Bit diameter / corner relief | Sets the size and style of the relief cut at each inside slot corner |
| Kerf / compensate | Laser beam width; when compensated, outlines are offset by half the kerf |
| Keep-out clearance | Gap left around keep-out bodies |
| Sheet size, margin, spacing | Sheet layout |
| Labels, label height | Single-stroke engraving of part and slot names |

---

## Self-hosting

3D2CNC is a single static `index.html`, with no build step and no backend. Serve it with any web server.

Or just open `index.html` locally in a browser.

The page loads these libraries from public CDNs, so it needs internet access on first load:

- [three.js](https://threejs.org/) r128: 3D preview
- [Clipper (clipper-lib)](https://www.npmjs.com/package/clipper-lib) 6.4.2: polygon boolean operations and offsets
- [JSZip](https://stuk.github.io/jszip/) 3.10.1: ZIP export
- Google Fonts: Barlow Semi Condensed, IBM Plex Sans and IBM Plex Mono (optional; system fonts are used if blocked)


---

## How it works

1. **Parse and weld.** Triangles are read from the file and duplicate vertices merged. Bodies are found by grouping triangles that share edges.
2. **Slice.** Each panel plane is intersected with the mesh, and the cut segments are joined into closed loops. Clipper turns those loops into clean polygons, and any keep-out regions are subtracted.
3. **Find crossings.** For every pair of panels that cross, the line where they meet is found in each panel's own coordinates. Where both panels have material along that line, a half-lap joint is cut: one slot from each side, meeting at the middle of the overlap.
4. **Relieve corners.** Dogbone or T-bone circles are added at the inside corners of each slot, sized to the bit.
5. **Check and repeat.** Small or unconnected pieces are removed, the joints that depended on them are cancelled, and the layout is rebuilt until nothing changes.
6. **Lay out and export.** Parts are packed onto sheets with their labels, then written to DXF and SVG.

---

## Limitations

- Sheet packing uses part bounding boxes, not true shapes.
- The model needs to be a closed (watertight) mesh. Gaps are bridged with straight lines and reported as a warning.
- Radial slicing always uses the vertical (Z) axis through the model's centre. Rotate the model to use a different axis.
- All parts are cut from a single stock thickness.

---

## Acknowledgements

Inspired by Autodesk 123D Make.
