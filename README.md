# openscad-claude-plugin

Claude Code plugin for parametric 3D modeling with OpenSCAD.

## Requirements

- [OpenSCAD](https://openscad.org/) installed and on `$PATH`
- Depends on the [`openscad-viewer`](https://www.npmjs.com/package/openscad-viewer) npm package

## Install

```bash
claude plugin marketplace add fingerskier/claude-plugins
claude plugin install openscad@fingerskier-plugins
```

## Features

- **Model Viewer MCP Tools** -- open and inspect .scad/.stl models from within Claude
  - `open` -- load a model file into the 3D viewer
  - `view` -- render the model from any camera angle and get a screenshot
- **OpenSCAD Language Skill** -- Claude knows the full OpenSCAD language: primitives, transforms, booleans, modules, functions, iteration
- **Parametric Design Skill** -- structured workflows for enclosures, brackets, mounts, and assemblies
- **Real-Time Feedback** -- the browser viewer updates automatically when Claude edits a .scad file

## How It Works

1. Claude opens a .scad or .stl file using the `open` MCP tool
2. A browser window opens showing the 3D model at `http://localhost:8439`
3. Claude edits the .scad file; the viewer auto-recompiles and updates
4. Claude uses the `view` tool to inspect the model from different angles
5. The browser camera animates to each view, so you see what Claude sees

## Skills

| Skill | Trigger | Description |
|-------|---------|-------------|
| model-viewer | Working with .scad/.stl files | How to use the viewer's open and view tools |
| openscad-language | Writing or editing .scad code | OpenSCAD language reference |
| parametric-design | Designing physical objects | Workflow for parametric parts and assemblies |

## License

[MIT](./LICENSE)
