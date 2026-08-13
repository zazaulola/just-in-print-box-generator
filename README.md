# Box.STL — Parametric Box Generator for 3D Printing

**Live: <https://zazaulola.github.io/just-in-print-box-generator/>**

A single-file, dependency-free web page that generates ready-to-slice binary STL
files of parametric boxes. Open `index.html` in any browser — it works fully
offline: geometry, 3D preview, and STL export all run client-side in plain
JavaScript.

## Features

- **Two shapes** — rectangular (with corner radius, 0 = sharp) or cylindrical
  (by diameter). Cylinders reuse the whole machinery: friction lids grip
  with four radial friction bumps, dividers become chords that stay inside
  the round wall, and wall smoothness adapts to the radius (up to a 96-gon).
  Hinged and sliding lids need straight walls and are disabled for cylinders.
- **Radial dividers** — cylinders can also be split into pie sectors: an
  equal count, or explicit spoke angles in degrees or % of the turn
  (e.g. `0, 120, 240`), with their own height and thickness.
- **Screw lid** — cylinders get a jar-style screw cap: a 2-start trapezoidal
  thread (4.5 mm pitch, 45° flanks, adjustable height) that closes in about
  half a turn; the fit clearance sets how freely it runs. Both parts print
  flat without supports.
- **Dimensions** — outer or inner (walls and floor are added automatically in
  inner mode), wall/floor thickness.
- **Bottom fillet** — a concave strength cove where the floor meets the
  walls. The outer base edge round follows from it automatically (capped by
  the wall thickness); a separate *flattening* parameter lifts that round off
  the bed when the box needs a straight bearing band to stand on. Both follow
  the outline (rectangles and cylinders alike) and apply to every box
  variant, including hinged, sliding, and screw.
- **Dividers** — split the cavity into zones along both axes: an equal split
  by zone count, or explicit divider positions in length units or % of the
  inner span (e.g. `40, 66%`). Crosswise and lengthwise dividers take
  separate heights (0 = full height, capped per lid type: below the lid's
  inner lip, below the sliding slot, and so on) and separate thicknesses. The data
  sheet lists the clear size of every resulting zone.
- **Cell merging** — a clickable zone map shows the divider grid from above:
  click any wall segment to remove it and merge the two cells it separates
  (click the faint mark to split them again). Any shape works — L-regions,
  long channels, a 2×2 block — and radial spokes merge sectors the same way.
  Merges live in the shareable link too, and the data sheet counts the
  resulting regions (e.g. zones 3 × 2 → 4).
- **Metric or imperial** — work in millimeters or inches (capacity in ml or
  fl oz, plastic in cm³/g or in³/oz); the geometry and the exported STL are
  always in millimeters, as slicers expect.
- **Shareable links & history** — every setting lands in the URL hash, so a
  link reproduces the exact setup (including the unit system), and the
  browser's back/forward buttons walk through your parameter changes.
- **Per-field reset** — every parameter shows a small ↺ button whenever it
  differs from its default (a modified-indicator that also resets the value,
  honoring the active unit system).
- **Six languages** — English, French, German, Spanish, Chinese, and Russian,
  including warnings and the data sheet, with locale-aware number formatting.
  The choice is remembered; first visits follow the browser language.
- **Four lid mechanisms** (plus none — an open tray):
  - **Friction** — a press-fit lid whose two grips are independent
    checkboxes: an *inner lip* that drops inside the box, an *outer skirt*
    that slides over its walls, or both at once — a double-wall lid that
    straddles the rim. Friction ridges reach 0.15 mm past the nominal
    clearance, so the lid holds with a light press fit at any clearance
    setting.
  - **Hinged** — a lid hinged along the top back edge, with a choice of
    hinge mechanism: *in place* (one pre-assembled closed piece, the pin grown
    inside the knuckles with 0.45 mm clearance — the first turn breaks it
    free), *filament pin* (parts print separately and flat; a piece of
    1.75 mm filament through a ⌀2.1 channel is the pin), or *snap-on* (the
    lid's C-clips press onto rods once and then rotate). Plus a choice of
    latch: a *swinging flap* on its own grown mini-hinge that hangs on a wall
    lug, a *springy tongue* that clicks over a rib, or none.
  - **Sliding** — a pencil-box lid that slides into side slots from one end,
    held shut by friction bumps, with a grip bar on top.
- **Lid body & skirt** — friction and hinged lids accept a body height
  (raised sides above the seam); the friction lid's lip and skirt depths
  set how far each grip reaches.
- **Live 3D preview** — a small software renderer on `<canvas>` (drag to
  rotate, scroll to zoom), with a build-plate grid and an axis triad.
- **Data sheet** — inner dimensions, capacity in ml, plastic volume and
  estimated PLA weight, triangle count.
- **Binary STL export** — millimeter units, watertight meshes; composite parts
  are unions of closed shells, which every mainstream slicer (PrusaSlicer,
  Orca, Cura) merges natively.

The panel asks its questions in dependency order — shape, then dimensions,
walls, lid, dividers — with checkboxes opening the optional sections and
nested radio groups showing what belongs to what. Sections fold away, and a
folded heading still names its current choice. Derived values (like the base
edge round) are stated rather than offered as knobs.

## Usage

Open the page, set the parameters, hit **Download STL**. Warnings block the
download when a combination cannot work (walls thicker than the box, a lid lip
deeper than the cavity, a box too short for the hinge, and so on).

### Printing notes

- Friction and sliding lids print flat as-is, no supports.
- The *in place* hinged box is exported assembled and closed. Pick your own
  print orientation — e.g. tilted ~45° around the long axis with supports
  where you like them; the working clearances (0.45 mm radial in the hinges,
  0.4 mm axial, 0.3 mm between lid and rim) are too narrow for supports to
  intrude. Consider a support blocker over the hinge area if your slicer is
  aggressive.
- The *filament pin* and *snap-on* hinged variants export two parts that both
  print flat; the filament variant's data sheet lists the pin length to cut.
- Friction fits assume typical FDM accuracy. If your printer runs tight or
  loose, tune the *Fit clearance* parameter — the ridges keep a constant
  0.15 mm engagement on top of it.

## How it works

Everything lives in `index.html`:

- Meshes are built directly as triangle lists — extruded rounded-rectangle
  outlines for tubs, plates, and lips, plus primitive builders (cuboid, prism,
  cylinder, tube) for the hinge hardware. No CSG: composite parts are
  overlapping closed shells welded by the slicer.
- Degenerate triangles from collapsed corner arcs (radius 0) are filtered
  before export, so meshes stay clean.
- Volumes are computed with the divergence theorem; the preview is a
  painter's-algorithm rasterizer with backface culling and height-shaded
  flat lighting.

No build step, no dependencies, no network access.
