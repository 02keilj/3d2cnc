# 3D2CNC

Turn a 3D model into flat parts that slot together, ready to cut on a CNC router or laser cutter.

3D2CNC is a browser-based take on Autodesk's discontinued 123D Make. Load an STL, OBJ or 3MF file, choose how to slice it, and export cut-ready DXF and SVG sheets. It adds the settings a real workshop needs: bit diameter, dogbones, slot clearance, laser kerf and single-stroke engraving labels.

**Live tool:** https://3d2cnc.nostranger.org

Everything runs in your browser, so your models are never uploaded anywhere.

---

## Features

### Construction methods
- **Crate (interlocked slices).** Two families of panels at right angles slot together egg-crate style. You can pick the slice directions (X/Y, X/Z or Y/Z), the panel count for each family and which family drops onto the other.
- **Radial.** Fins radiate around the vertical axis and slide onto stacked horizontal rings. This suits vases, pots and other turned shapes. The hub radius is calculated automatically so neighbouring fins don't collide.
- **Stacked layers.** The model is sliced into layers one stock-thickness apart, to glue together in order.
- **Fins (parametric wall art).** A stacked-layer pattern that leaves gaps between layers: 1-layer gaps (every second layer removed), 2-layer gaps, or a custom gap in mm. The fins are threaded onto two dowels that run through every fin, with spacer washers cut from the same stock to fill each gap (two per gap for 2-layer gaps). Washer size is automatic or set by hand. If a custom gap isn't a whole number of layers, you get a warning suggesting the nearest gap or tube spacers. The *Wave wall art* sample shows the idea. Turn labels off if the fin faces will be on show.

### Art generator (separate mode)
Switch between **Slicer** and **Art generator** at the top of the side panel. The generator has its own settings and a large preview, and nothing is sliced until you ask:
- **Patterns:** water droplets (interfering ripples), waves, cloth folds, sand dunes and hills.
- **Pattern settings:** features (how many drops, folds or crests) and a seed. Each seed gives a different design; step through seeds with ◀ Seed / Seed ▶, or let **Surprise me** pick a pattern, seed and feature count.
- **Panel:** width, height, back depth (the solid strip where the dowels run) and relief depth.
- **Fins:** direction (vertical or horizontal), stock thickness and gap (1 or 2 layers). The preview shades the gaps so you can see how the fins will read.
- The preview updates as you change any setting.
- **Slice into fins →** sends the panel to the slicer as stacked fins with dowels and spacer washers, using your fin settings, and switches to the Design step.
- Saved projects and share links store only the generator settings, so links stay short and reload exactly.

### Layout options
- **Panel spacing**: an even count, a fixed distance (one panel every N mm), or adaptive. Adaptive keeps the same count but packs panels closer where the cross-section changes quickly and spreads them out on flat stretches, always at least two stock thicknesses apart.
- **Grid rotation**: turn the crate grid to any angle around the vertical axis, for example 45° for a diamond lattice. Dragging, snapping and adding panels all still work.

### Base and wall mounting
- **Display base**: a base plate under the model, shaped as the footprint, a rectangle, a circle or the bottom outline, with a margin. Panels that reach the bottom get tabs that pass through matching slots in the base. Tabs are placed between joint crossings, never on them.
- **Half model**: cut the model flat across X, Y or Z at any position (slider or exact mm) and keep either side.
- **Back plate**: on a half model, a plate on the cut face (shaped as the cut outline, hull or rectangle) that the panels tab into. Optional keyhole slots for hanging (head size, slot width, slot length, count and height up the plate) are centred in the gaps between panels and kept well inside the plate edge, so they're hidden behind the model and the screw head has room. Panels that meet the plate at an angle get correspondingly wider slots.

### Joints that work in material
- Slot width is the stock thickness plus a clearance you set.
- **CNC corner relief:** dogbone, T-bone (deeper), T-bone (wider) or none, sized from your bit diameter.
- **Laser mode** has optional kerf compensation.
- **Edge cases are handled.** If two panels only graze each other, the crossing gets a one-sided notch instead of a weak joint. Joints that would leave a thin sliver are skipped. A slot can still break out of a panel edge as a clean rebate, for example on a panel sitting on the floor.
- **Fit test pieces.** An optional comb strip with slots that step through a clearance range, each slot engraved with its value, plus a loose test tab. Cut it once per sheet of material to find the clearance that fits.
- **Crush ribs** (laser). Small rounded bumps on each slot wall, where the mating panel sits, give a snug glue-free fit even when sheet thickness varies.
- **Collision check.** After the joints are cut, every pair of non-parallel panels is tested for material that still overlaps. Colliding panels turn red in the 3D view and are listed in the warnings.
- **Fragments are cleaned up.** Small pieces under a minimum size are removed, as are pieces with no joints (optional). Joints that pointed at a removed piece are cancelled and the layout is rebuilt until it is stable.

### Making parts lighter
- **Lightening cut-outs** (crate and radial). Windows are cut in a grid, leaving a rim and webs of a set width. Window corners are rounded to suit the bit, and slots keep solid material around them.
- **Hollow stacked layers.** Layers are hollowed to a wall thickness, with a chosen number of solid layers at the bottom and top. Each layer's wall is checked against the layers above and below it, so sloping sides keep full-thickness walls.
- **Registration outlines** (stacked). The outline of the layer above is engraved on each layer's top face, on an `ENGRAVE` layer, so glue-ups line up by eye.
- **Alignment dowels** (stacked). Long rods threaded through the layers, planned per piece: where a layer is cut into several separate pieces (a body, a tail, two wings), every piece gets its own holes, two by default, or one if it's too small for two. Dowels that hold the most pieces still short of holes are chosen first, and the longest of those, so an existing dowel is extended through more layers rather than a new one added. Dowels made redundant by longer ones are dropped. Small pieces get their holes closer together; big ones keep them well apart so they can't turn. Hidden ends (on by default) stop each dowel one layer short of any exposed face. Each dowel's length and layer range is listed in the parts notes. Any pieces left with one hole or none are named in the warnings.
- **Edit dowels by hand.** Press **Edit dowels** in the 3D view. The layers turn see-through; click a dowel to remove it, or click a layer to add a dowel at that spot, running through every layer it can reach from there. Hand-added dowels show in green and are kept every time the layout updates. Removed spots stay clear of automatic dowels, and the remaining pieces are re-planned around your changes. Undo, redo and **Reset dowels** work as usual, and edits are saved in projects and share links.

### Keep-out bodies
Include extra bodies in your file, such as a lid that sits in a recess or an insert panel. Give each body a role:

| Role | Effect |
|---|---|
| Slice | Normal geometry that gets sliced |
| Keep-out | Panels are trimmed clear of it through their full thickness, plus a clearance |
| Keep-out + cut part | As above, and the body is also exported as a flat part (K1, K2, …) |
| Ignore | Left out entirely |

**Locking tabs:** with this on, panels running beneath a cut insert get a tab rising through it, flush with its top face, and the insert gets matching slots (with dogbones on a CNC router). The insert then drops onto the tabs and can't slide around.

A body counts as separate when its surface is closed on its own, so a lid resting in its recess stays separate even where the two share corner points.

### Interactive assembly view
- Orbit the 3D preview, with a transparent model shown behind it.
- **Drag panels to move them.** Crate panels and rings slide along their normal and radial fins rotate around the axis. Joints regenerate when you release.
- Use **To floor** and **To top** (or the −/+ ends of the axis) to place a selected panel flush with the edge of the model.
- **Add or delete panels.** Select a panel, then add another of the same family halfway to its neighbour, or delete it.
- **Reset layout** undoes every move, addition and deletion.
- **Build steps**: step through the assembly one panel at a time (◀ ▶ or the arrow keys) in build order, with each new panel highlighted and a one-line instruction.
- **Identical shapes** are detected and grouped in the parts list, the assembly notes and the CSV (`same_shape_as`), for example "R1 … R12 (×12)".
- **Undo and redo** for all panel edits: use the buttons, Ctrl+Z, or Ctrl+Shift+Z / Ctrl+Y.
- The **Lift** slider separates the sliding family so you can check the assembly direction.
- Click a part to highlight it in the 3D view, the sheet layout and the parts list together.

### Output
- **All parts DXF / All parts SVG**: every part in a single file, spaced out in a roughly square block and ready to arrange and nest in your CAM software. This is the main export.
- **Sheets ZIP** (after nesting): one DXF and one SVG per nested sheet, plus a CSV cut list.
- **Each part is grouped with its own labels.** In the SVG every part is a `<g>` named after it. In the DXF every part is a named block (optional, on by default; untick it if your CAM can't read blocks). Parts stay together when you re-nest or move them.
- DXF layers:
  - `CUT_OUTER`: outer profiles; cut outside the line.
  - `CUT_INNER`: holes; cut inside the line.
  - `LABELS`: single-stroke engraving, as open polylines.
  - `ENGRAVE`: registration outlines on stacked layers.
  - `SHEET`: optional sheet outline, for reference only.
- SVG colours are red for outer cuts, blue for inner cuts and black for engraving.
- Each part is engraved with its own name. Each slot is labelled with the name of the panel that fits into it.
- Add holding tabs in your CAM software.

### Nesting (optional)
Nesting runs only when you click **Run nesting** in the Export step:
- **Shape-based grid nesting.** Parts are packed by their actual shapes at a resolution of 1, 2 or 3 mm, so concave parts interlock.
- **Rotations.** Choose none, or steps of 90°, 45°, 15°, 5° (the default) or 2°. Finer steps let long, thin parts lie down or angle into gaps, at the cost of a few extra seconds.
- **Method.** *Quick* does one pass. *Deep* works like [Deepnest](https://deepnest.io): a genetic algorithm keeps trying new part orders and rotations in a background thread, shows each better layout as it finds it, and stops after the time you choose (30 s to 10 min, or until you press Stop). It scores layouts by sheet count, then by how small the last sheet's used area is. For true-shape nesting in Deepnest itself, load the **All parts SVG** export.
- **Sheet use** is the box around all parts on a sheet as a share of the sheet, so it shows how much stock is no longer usable as an offcut.
- **Settle on exact outlines** (on by default). After packing, every part slides down and then left until its true outline sits exactly one part spacing from its neighbours or the margin. This removes the grid's rounding slack (2–3 mm per neighbour at Normal accuracy) and can never create an overlap.
- **Grain.** *Along parts* or *Across parts* only turns parts 0° or 180°, so each part's long side runs with (or across) the grain, which is taken to run along the sheet's long side.
- **Offcuts to use first.** Enter leftover pieces as width × height × count, for example `600x400, 900x300x2`, with the width in the same direction as the full sheet's width. Nesting fills them, smallest first, before starting a full sheet; Deep nesting treats them as free stock. They show as *offcut* sheets and in the ZIP file names.
- **Keep the offcut.** *Full length* packs across the short side of the sheet and leaves a long strip of stock. *Full width* packs from one end and leaves a shorter, wider piece.
- **Parts inside holes.** Small parts go into the holes of bigger ones first, such as hollow-layer centres, ring centres and lightening windows.
- **Fill the first sheet first.** Each part takes the lowest, then leftmost, free spot on the earliest sheet with room before a new sheet is started.
- **Part spacing** defaults to 3 × bit diameter (2 mm on a laser) and follows the bit until you set your own value. Edge margin is also adjustable.
- **Background processing.** Nesting runs in a background worker, typically in under a second. Changing the parts or any nesting setting marks the result out of date until you run it again.

### Projects
- **Save project** writes one `.3d2cnc.json` file containing the model, every setting, body roles, rotation, and all panel moves, additions and deletions. Load it with **Load project**, or drop it onto the viewer.
- **Share link** packs the same project into the link itself (compressed, after the `#`), so nothing is uploaded anywhere. Projects using a built-in sample give a short link; an uploaded model is carried inside the link, which can get long. Some chat and email apps cut very long links short, so send the project file for big models. Opening the link loads the project straight away.

### Other
- Load STL (binary or ASCII), OBJ or 3MF, including 3MF files with components and multiple parts.
- Use the model at its file size or scale it to fit a given width, depth or height.
- Rotate the model in 90° steps.
- Light, dark or automatic theme.
- Sample models are included for quick testing.

---

## Quick start

The slicer has two steps, switched at the top of the panel. (The **Art generator** tab beside **Slicer** makes fin wall art; see above.)

**1 · Design**
1. Load your model, or start from one of the samples.
2. Set the **stock thickness** and either your **bit diameter** (CNC) or **kerf** (laser).
3. Choose **Crate**, **Radial** or **Stacked**, and the number of panels.
4. Check the **Assembly** view. Drag, add or delete panels if needed, and read any warnings in the bar at the bottom.

**2 · Export**
1. Set the labels you want.
2. Then either:
   - click **All parts DXF** (or SVG) and nest in your CAM or laser software, or
   - set your sheet size and spacing, click **Run nesting**, check the **Sheets** tab and download the **Sheets ZIP**.

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

## How it works

1. **Parse and weld.** Triangles are read from the file and duplicate vertices merged. Bodies are found by grouping triangles that share edges.
2. **Slice.** Each panel plane is intersected with the mesh, and the cut segments are joined into closed loops. Clipper turns those loops into clean polygons, and any keep-out regions are subtracted.
3. **Find crossings.** For every pair of panels that cross, the line where they meet is found in each panel's own coordinates. Where both panels have material along that line, a half-lap joint is cut: one slot from each side, meeting at the middle of the overlap.
4. **Relieve corners.** Dogbone or T-bone circles are added at the inside corners of each slot, sized to the bit.
5. **Check and repeat.** Small or unconnected pieces are removed, the joints that depended on them are cancelled, and the layout is rebuilt until nothing changes.
6. **Export.** Parts are written to DXF and SVG with their labels, either all together in one file or, after nesting, sheet by sheet. Nesting rasterises each part (grown by half the spacing) at every allowed rotation and packs them biggest-first wherever the top edge stays lowest (on a sheet turned to suit the offcut setting), trying holes in already-placed parts first.

---

## Limitations

- Nesting places parts on a grid (1–3 mm). With **Settle parts on their exact outlines** on, spacing comes out within a fraction of a millimetre of the setting; with it off, gaps can be up to about one grid cell wider.
- The model needs to be a closed (watertight) mesh. Gaps are bridged with straight lines and reported as a warning.
- Radial slicing always uses the vertical (Z) axis through the model's centre. Rotate the model to use a different axis.
- All parts are cut from a single stock thickness.
