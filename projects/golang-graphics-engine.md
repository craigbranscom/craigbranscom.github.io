---
# layout: post
# title: Golang Game Engine
---

## Resources
GitHub: [engine-sandbox-golang](https://github.com/craigbranscom/engine-sandbox-golang)

## Engine Subsystems

Importer: Builds the node tree from an existing scene YAML file.
Pipeline: Manages the render pipeline.
Node Tree: Handles tree-wide functions.
Nodes: Prebuilt nodes constructed from components, placed in the node tree to describe the scene.
Components: Composable data packages for constructing nodes.

## Controls

Camera Movement: Use WASD keys to pan the camera.
Object Rotation: Use UI, JK, and NM keys to rotate objects along the X, Y, and Z axes respectively.
