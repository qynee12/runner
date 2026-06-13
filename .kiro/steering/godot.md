# Godot Engine — General Project Guidance

This project uses **Godot 4.x**. Follow these principles when reasoning about architecture, scene structure, and engine usage.

---

## Core Concepts

### Scenes, Nodes, and the Scene Tree
- A **game is a tree of nodes** grouped into reusable **scenes**.
- Scenes are flexible: they serve as both prefabs and levels. Nest them freely.
- **Nodes** are the smallest building blocks. Combine them to form scenes, then combine scenes into the scene tree.
- Use **2D nodes** (`Node2D`, `CharacterBody2D`, `Sprite2D`, etc.) for 2D games; **3D nodes** (`Node3D`, `CharacterBody3D`, etc.) for 3D. The base unit for 2D is pixels.
- Scenes can inherit from other scenes — prefer composition and inheritance over duplicating logic.

### Signals
- Use **signals** for node-to-node communication. Avoid hard-wiring node references across unrelated branches of the scene tree.
- Name signals in the **past tense** (`door_opened`, `score_changed`).
- Connect signals in `_ready()` using `signal_name.connect(callable)`.
- Define custom signals at the top of a script with `signal my_signal`.

### Object Hierarchy (lightweight alternatives to nodes)
When a full `Node` is overkill, consider lighter objects:
- **`Object`** — manual memory management, most lightweight.
- **`RefCounted`** — reference-counted, auto-freed; good for custom data classes.
- **`Resource`** — serializable, Inspector-compatible; ideal for data assets (stats, config, item definitions).

### Scenes vs. Scripts
- Use **scenes** (`.tscn`) to declare node hierarchies and composition.
- Use **scripts** (`.gd`) to add behavior with imperative code.
- Large node hierarchies should be scenes, not built entirely in script — scenes instantiate faster than equivalent script-based construction.
- Register named types with `class_name` for cleaner creation dialogs and runtime access.

---

## Project Structure Conventions

- Organize files by feature or system, not by type alone.
- Keep scene files (`.tscn`), scripts (`.gd`), and assets co-located where practical.
- Use `res://` paths for all resource loading; never use absolute paths.
- Use `@export` to expose values to the editor instead of hardcoding them in scripts.
- Use `@onready` to cache node references after the scene tree is ready.

---

## Editor & Tooling

- The Godot editor itself is a Godot game — use `@tool` at the top of a script to run it in the editor context (useful for plugins and custom inspectors).
- Hot-reload is supported: changes to scripts and scenes apply without restarting the game during development.
- Use the built-in debugger, profiler, and remote inspector for performance analysis before optimizing.

---

## Performance Guidelines

- Avoid creating and deleting many nodes per frame — pool or reuse objects.
- Use `_physics_process(delta)` for physics and movement; use `_process(delta)` only for non-physics per-frame logic.
- Prefer `Area2D`/`Area3D` for overlap detection over polling positions every frame.
- Large numbers of simple objects (bullets, particles) should use `MultiMeshInstance` or `GPUParticles` rather than individual nodes.
- Profile before optimizing — use Godot's built-in profiler.

---

## Input Handling

- Define input actions in **Project Settings → Input Map** rather than hardcoding key constants.
- Read inputs with `Input.is_action_pressed("action_name")` or handle them in `_unhandled_input(event)` / `_input(event)`.
- Use `_unhandled_input` for game input so UI elements can consume events first.

---

## Source Control

- The `.godot/` directory contains editor-generated cache files. It can be excluded from version control, but committing it speeds up first-load times for collaborators.
- Always commit `.tscn`, `.gd`, `.tres`, and `project.godot` files.
- Use the provided `.gitattributes` to ensure consistent line endings.

---

## References
- [Godot Docs — Key Concepts](https://docs.godotengine.org/en/stable/getting_started/introduction/key_concepts_overview.html)
- [Godot Design Philosophy](https://docs.godotengine.org/en/stable/getting_started/introduction/godot_design_philosophy.html)
- [Scenes vs Scripts](https://docs.godotengine.org/en/stable/tutorials/best_practices/scenes_versus_scripts.html)
- [When to Avoid Nodes](https://docs.godotengine.org/en/stable/tutorials/best_practices/node_alternatives.html)
