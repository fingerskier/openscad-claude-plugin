---
name: model-viewer
description: "Open, view, and inspect 3D models (.scad and .stl files) using the openscad-viewer MCP tools. Use when working with OpenSCAD files, STL files, 3D models, or when the user wants to see or inspect a model from different angles."
---

# Model Viewer - 3D Model Inspection

View and inspect OpenSCAD (.scad) and STL (.stl) 3D models using the `openscad-viewer:` MCP tools.

## Prerequisites

- OpenSCAD must be installed and on `$PATH` (for .scad files; not needed for .stl)
- The openscad-viewer MCP server must be running

## Starting the Viewer Manually

If the MCP server is not already connected, start it manually:

```bash
npx openscad-viewer <file.scad|file.stl>
```

This opens a browser window at `http://localhost:8439` showing the 3D model.
The user sees the same view the agent is inspecting.

## MCP Tools

### `openscad-viewer:open`

Opens a .scad or .stl file in the viewer. Use this to load or switch models.

**Parameters:**
- `file` (required): Absolute or relative path to a .scad or .stl file

**Returns:**
```json
{
  "success": true,
  "file": "/absolute/path/to/model.scad",
  "fileSize": 2048,
  "boundingBox": { "min": [-10, -10, 0], "max": [10, 10, 20] }
}
```

The bounding box tells you the model's dimensions. Use it to:
- Understand the scale of the model
- Calculate appropriate camera distance
- Verify geometry changes after edits

### `openscad-viewer:view`

Renders the current model at a specified camera angle and returns a PNG screenshot.

**Parameters:**
- `azimuth` (optional, default 45): Horizontal angle 0-360 degrees
- `elevation` (optional, default 30): Vertical angle -90 to 90 degrees
- `distance` (optional): Distance from model center. Auto-calculated from bounding box if omitted.

**Returns:**
```json
{
  "imagePath": "/tmp/openscad-viewer-capture-xxxxx.png",
  "camera": { "azimuth": 45, "elevation": 30, "distance": 100 }
}
```

Read the returned `imagePath` to see the rendered model.

**Side effect:** The browser view animates to the requested angle, so the user sees what you are looking at.

## Standard Views Quick Reference

| View | Azimuth | Elevation | Use For |
|------|---------|-----------|---------|
| Front | 0 | 0 | Width and height |
| Back | 180 | 0 | Rear features |
| Left | 270 | 0 | Left side profile |
| Right | 90 | 0 | Right side profile |
| Top | 0 | 90 | Footprint / layout |
| Bottom | 0 | -90 | Base features |
| Isometric | 45 | 30 | General 3D overview |
| High iso | 45 | 60 | Top-heavy features |

## Inspection Workflow

When inspecting or verifying a model:

1. **Front view**: `view { "azimuth": 0, "elevation": 0 }`
2. **Top view**: `view { "azimuth": 0, "elevation": 90 }`
3. **Isometric view**: `view { "azimuth": 45, "elevation": 30 }` (default)
4. **Right side**: `view { "azimuth": 90, "elevation": 0 }`

## Edit-View Cycle

When modifying OpenSCAD code:

1. Edit the .scad file
2. The viewer auto-recompiles and updates (file watching is built in)
3. Use `view` to verify the change from relevant angles
4. If there is a compilation error, the viewer keeps the last good model visible and shows the error
5. Fix the error and save again — the viewer updates automatically

## Tips

- You do not need to call `open` after editing a file — the viewer watches for changes automatically
- Call `open` only when switching to a different file
- The bounding box from `open` helps estimate appropriate camera distances
- Use elevation 90 (top-down) to check X/Y layout, elevation 0 for Z height
- Auto-distance works well for most models — only set distance manually for close-up details
