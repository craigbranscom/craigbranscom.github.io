---
# layout: post
# title: Golang Game Engine
---

<!-- # Introduction -->
The "engine-sandbox-golang" repository by Craig Branscom is an open-source project designed to provide a sandbox environment for experimenting with engine subsystems using Golang (Go). This project is geared towards developers interested in game development, graphics, or general engine architecture.

## Resources
GitHub: [engine-sandbox-golang](https://github.com/craigbranscom/engine-sandbox-golang)

## Engine Subsystems

Importer: Builds the node tree from an existing scene YAML file.
Pipeline: Manages the render pipeline.
Node Tree: Handles tree-wide functions.
Nodes: Prebuilt nodes constructed from components, placed in the node tree to describe the scene.
Components: Composable data packages for constructing nodes.
Setup and Usage

## Installation

Install Go: Ensure Go is installed on your machine.
Clone the Repo: Use git clone to get a local copy.
Download Dependencies: Run go get to install project dependencies.
Run the Project: Execute go run main.go.

## Controls

Camera Movement: Use WASD keys to pan the camera.
Object Rotation: Use UI, JK, and NM keys to rotate objects along the X, Y, and Z axes respectively.
Example Shaders

# Shaders

Fragment Shader: Defines the color output of pixels.
Vertex Shader: Manages vertex transformations and camera positioning.

