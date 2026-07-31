# Box.STL — Parametric Box Generator for 3D Printing

A single-file, dependency-free web page that generates ready-to-slice binary STL
files of parametric boxes. Open `index.html` in any browser — it works fully
offline: geometry, 3D preview, and STL export all run client-side in plain
JavaScript.

## Features

- **Dimensions** — outer or inner (walls and floor are added automatically in
  inner mode), wall/floor thickness, corner radius (0 = sharp corners).
- **Four lid options:**
  - **None** — an open tray.
  - **Inset** — a flat lid with a hollow lip that drops inside the box.
    Friction ridges on the lip reach 0.15 mm past the nominal clearance, so the
    lid holds with a light press fit at any clearance setting.
  - **Cap** — a lid that slides over the outside of the box, with the same
    friction ridges on the inside of its skirt.
  - **Hinge + latch** — a print-in-place design exported as one pre-assembled
    piece: a flat lid hinged along the top back edge, plus a swinging latch
    flap on its own mini-hinge at the front that hangs on a lug in the wall and
    locks the lid (flip it up to open). The hinge pins are grown inside the
    knuckles with 0.45 mm radial clearance — the first turn breaks them free.
    No screws, no pins, no assembly.
- **Live 3D preview** — a small software renderer on `<canvas>` (drag to
  rotate, scroll to zoom), with a build-plate grid and an axis triad.
- **Data sheet** — inner dimensions, capacity in ml, plastic volume and
  estimated PLA weight, triangle count.
- **Binary STL export** — millimeter units, watertight meshes; composite parts
  are unions of closed shells, which every mainstream slicer (PrusaSlicer,
  Orca, Cura) merges natively.

## Usage

Open the page, set the parameters, hit **Download STL**. Warnings block the
download when a combination cannot work (walls thicker than the box, a lid lip
deeper than the cavity, a box too short for the hinge, and so on).

### Printing notes

- Inset and cap lids print flat as-is, no supports.
- The hinged box is exported assembled and closed. Pick your own print
  orientation — e.g. tilted ~45° around the long axis with supports where you
  like them; the working clearances (0.45 mm radial in the hinges, 0.4 mm
  axial, 0.3 mm between lid and rim) are too narrow for supports to intrude.
  Consider a support blocker over the hinge area if your slicer is aggressive.
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
