---
name: openscad-language
description: "Write and edit OpenSCAD code for parametric 3D modeling. OpenSCAD language reference including primitives, transforms, boolean operations, modules, functions, and best practices. Use when creating .scad files, editing OpenSCAD code, or doing parametric/programmatic CAD."
---

# OpenSCAD Language Reference

OpenSCAD is a script-based 3D CAD modeler. Models are defined programmatically — there is no interactive GUI modeling. Files use the `.scad` extension.

## Core Concepts

- OpenSCAD is **declarative** — you describe geometry, not steps
- Everything is either a **2D shape**, a **3D solid**, a **transformation**, or a **boolean operation**
- The final geometry is whatever is in the top-level scope (not inside a module)
- Use `$fn` to control facet count (smoothness) for curved surfaces

## 3D Primitives

```scad
cube([x, y, z], center = true);
sphere(r = 5);                    // or sphere(d = 10);
cylinder(h = 10, r = 5);          // or r1, r2 for cone
cylinder(h = 10, r1 = 5, r2 = 0); // cone
```

## 2D Primitives (for extrusion)

```scad
circle(r = 5);
square([10, 20], center = true);
polygon(points = [[0,0], [10,0], [5,10]]);
text("Hello", size = 10);
```

## Extrusions (2D to 3D)

```scad
linear_extrude(height = 10) circle(r = 5);     // cylinder
linear_extrude(height = 10, twist = 90) square([10, 2], center = true);
rotate_extrude() translate([10, 0]) circle(r = 3);  // torus
```

## Transformations

```scad
translate([x, y, z]) child();
rotate([rx, ry, rz]) child();     // degrees around each axis
scale([sx, sy, sz]) child();
mirror([1, 0, 0]) child();        // mirror across YZ plane
color("red") child();
color([0.5, 0.5, 0.5, 0.8]) child();  // RGBA
```

## Boolean Operations

```scad
union() { a(); b(); }          // combine shapes
difference() { a(); b(); }     // subtract b from a (first child is base)
intersection() { a(); b(); }   // keep only overlap
```

## Modules (reusable components)

```scad
module bolt(diameter, length) {
    cylinder(h = length, d = diameter);
    translate([0, 0, length])
        cylinder(h = diameter * 0.6, d = diameter * 1.8);
}

bolt(6, 20);
translate([15, 0, 0]) bolt(8, 30);
```

## Variables and Functions

```scad
wall = 2;
inner_r = 10;
outer_r = inner_r + wall;

function circumference(r) = 2 * PI * r;
```

**Note:** Variables in OpenSCAD are set at compile time, not at runtime. A variable can only be assigned once per scope.

## Iteration

```scad
// Linear pattern
for (i = [0:5]) translate([i * 10, 0, 0]) cube(5);

// Circular pattern
for (a = [0:60:359]) rotate([0, 0, a]) translate([20, 0, 0]) cylinder(h=5, r=2);

// Grid
for (x = [0:10:50], y = [0:10:30]) translate([x, y, 0]) cube(3);
```

## Conditionals

```scad
module mount(type = "round") {
    if (type == "round") cylinder(h = 5, r = 10);
    else cube([20, 20, 5], center = true);
}
```

## Special Variables

| Variable | Purpose | Typical Value |
|----------|---------|---------------|
| `$fn` | Fixed facet count for curves | 32-128 for final, 16 for preview |
| `$fa` | Minimum angle per facet | 12 (default) |
| `$fs` | Minimum facet size (mm) | 2 (default) |
| `$t` | Animation parameter (0-1) | - |

## Import / Include

```scad
include <library.scad>    // include definitions AND execute top-level geometry
use <library.scad>         // include only module/function definitions
import("part.stl");        // import STL geometry
```

## Hull and Minkowski

```scad
hull() { sphere(5); translate([20,0,0]) sphere(5); }  // convex hull
minkowski() { cube(10); sphere(2); }                    // rounded cube
```

## Common Patterns

### Rounded box
```scad
module rounded_box(size, radius) {
    minkowski() {
        cube([size.x - 2*radius, size.y - 2*radius, size.z - radius], center = true);
        sphere(r = radius);
    }
}
```

### Shell (hollow solid)
```scad
module shell(outer_size, wall) {
    difference() {
        cube(outer_size, center = true);
        cube([outer_size.x - 2*wall, outer_size.y - 2*wall, outer_size.z], center = true);
    }
}
```

### Mounting hole pattern
```scad
module mounting_holes(spacing, hole_d, depth) {
    for (x = [-1, 1], y = [-1, 1])
        translate([x * spacing/2, y * spacing/2, 0])
            cylinder(h = depth, d = hole_d, center = true);
}
```

### Pipe / tube
```scad
module pipe(h, outer_r, wall) {
    difference() {
        cylinder(h = h, r = outer_r);
        translate([0, 0, -0.1])
            cylinder(h = h + 0.2, r = outer_r - wall);
    }
}
```

## Best Practices

1. **Define dimensions as variables at the top** — never use magic numbers
2. **Derive dependent dimensions** — `outer_r = inner_r + wall`
3. **Use modules** for any shape used more than once
4. **Use `center = true`** for primitives that will be differenced or intersected
5. **Set `$fn` globally** or per-shape — higher values = smoother but slower
6. **Use `difference()` for holes** — the first child is the base, subsequent children are subtracted
7. **Test with low `$fn` (16-24)**, increase for final output (64-128)
8. **Keep the top-level clean** — put geometry in modules, instantiate at top level

## Debug Modifiers

- `#` — highlight (transparent red overlay): `#cylinder(h=5, r=2);`
- `%` — transparent preview: `%cube(20);`
- `!` — show only this shape: `!cylinder(h=5, r=2);`
- `*` — disable (skip rendering): `*cube(10);`
