---
name: parametric-design
description: "Design parametric 3D models with OpenSCAD. Workflows for creating enclosures, mechanical parts, and assemblies. Use when the user asks to design, create, or model a physical object, enclosure, bracket, mount, or mechanical part."
---

# Parametric Design Workflow

A structured approach to creating parametric 3D models with OpenSCAD and the model viewer.

## Design Process

### 1. Understand the Requirements

Before writing any code, clarify:
- Overall dimensions (or constraints)
- Mounting/interface requirements (screw holes, snap fits, mating surfaces)
- Manufacturing method (3D printing, CNC, laser cut)
- Material and minimum wall thickness
- Tolerances for mating parts

### 2. Set Up the File

```scad
// [Part Name] - [brief description]
// Units: mm

// === Parameters ===
width = 50;
depth = 30;
height = 20;
wall = 2;
corner_r = 2;

// === Derived ===
inner_w = width - 2 * wall;
inner_d = depth - 2 * wall;
inner_h = height - wall;

// === Quality ===
$fn = 32;

// === Assembly ===
main_body();
```

### 3. Build Incrementally

1. Start with the outer shell / bounding volume
2. Open the file: `openscad-viewer:open { "file": "part.scad" }`
3. Verify dimensions with `view` from front and top
4. Add internal features (cavities, channels, ribs)
5. Verify with isometric and section views
6. Add mounting features (holes, bosses, snap fits)
7. Verify from all relevant angles

### 4. Edit-View-Verify Cycle

After each significant change:
1. Save the .scad file (viewer auto-recompiles)
2. Check bounding box in `open` result for dimension sanity
3. View from the angle most relevant to the change:
   - Added a hole? View from the axis the hole runs along
   - Changed width? View from front (azimuth=0, elevation=0)
   - Added features on top? View from above (elevation=90)

## Common Design Tasks

### Enclosure / Box

```scad
module enclosure(size, wall, lip_h) {
    difference() {
        cube(size, center = true);
        translate([0, 0, wall])
            cube([size.x - 2*wall, size.y - 2*wall, size.z], center = true);
    }
}

module lid(size, wall, lip_h) {
    // Outer lid
    cube([size.x, size.y, wall], center = true);
    // Inner lip
    translate([0, 0, -lip_h/2])
        difference() {
            cube([size.x - 2*wall + 0.2, size.y - 2*wall + 0.2, lip_h], center = true);
            cube([size.x - 4*wall, size.y - 4*wall, lip_h + 0.1], center = true);
        }
}
```

### L-Bracket

```scad
module l_bracket(w, h, d, t, hole_d) {
    difference() {
        union() {
            cube([w, t, h]);           // vertical
            cube([w, d, t]);           // horizontal
        }
        // mounting holes
        for (x = [w*0.25, w*0.75]) {
            translate([x, d/2, -1])
                cylinder(h = t+2, d = hole_d);
            translate([x, -1, h/2])
                rotate([-90, 0, 0])
                    cylinder(h = t+2, d = hole_d);
        }
    }
}
```

### Threaded Insert Boss

```scad
module insert_boss(outer_d, inner_d, height) {
    difference() {
        cylinder(h = height, d = outer_d);
        cylinder(h = height + 0.1, d = inner_d);
    }
}
```

## 3D Printing Considerations

| Constraint | Guideline |
|-----------|-----------|
| Min wall thickness | 1.2mm (2-3 perimeters at 0.4mm nozzle) |
| Hole tolerances | Add 0.2-0.4mm to nominal diameter for FDM |
| Overhangs | Keep under 45 degrees or add supports |
| Bridge length | Under 10mm unsupported |
| Layer orientation | Strongest perpendicular to layer lines |
| Mating clearance | 0.3-0.5mm gap for FDM |
| Min feature size | 0.8mm for FDM |

## Multi-Part Assemblies

When designing parts that fit together:

1. Define shared dimensions in a `config.scad` file
2. `include <config.scad>` in each part file
3. Use `openscad-viewer:open` to switch between parts during design
4. Test mating dimensions from appropriate angles

## Tips

- Start with `$fn = 16` for fast iteration, increase to 64+ for final
- Use `#` prefix to highlight a shape in debug: `#cylinder(h=5, r=2);`
- Use `%` prefix for transparent preview: `%cube(20);`
- Use `!` prefix to show only one shape: `!cylinder(h=5, r=2);`
- Use `echo()` to print computed dimensions to the console for debugging
- Add 0.1mm to `difference()` cutters to avoid coincident faces
