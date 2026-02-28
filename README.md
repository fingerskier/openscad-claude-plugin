# openscad-claude-plugin
Claude-code plugin for generating parametric models

## Requirements
* OpenSCAD pre-installed

## Features
* Model viewer
  * Renders the .scad
  * Watches for changes and update the model
  * Allows camera adjustments
  * Can a new view angle and distance via MCP (i.e. when the agent requests renderings the user sees where it's looking)

## Implementation
* Pre-edit hook: When the user requests an interaction with a .scad file it opens the model-viewer (if not already opened)
* MCP tools
  * `update` ~ sends the new .scad file to the model-viewer
  * `view` ~ gets a rendered image of the model from a particular angle + distance
 
