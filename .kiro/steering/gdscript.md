# GDScript Style & Conventions

All GDScript in this project follows the [official GDScript style guide](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_styleguide.html). This file is the authoritative reference for code style decisions.

---

## Formatting

- **Encoding:** UTF-8, no BOM, LF line endings.
- **Indentation:** Tabs (not spaces). One tab per indent level.
- **Continuation lines:** Use 2 indent levels for statement continuations; 1 indent level for arrays, dicts, and enums.
- **Line length:** Keep lines under 100 characters; aim for 80 where practical.
- **Blank lines:** Two blank lines between top-level functions and class definitions. One blank line to separate logical sections inside a function.
- **One statement per line.** Never combine `if condition: do_thing` on one line. Exception: ternary expressions.
- **Trailing commas** on the last item of multiline arrays, dicts, and enums.
- **Boolean operators:** Use `and`, `or`, `not` — not `&&`, `||`, `!`.
- **Quotes:** Double quotes by default. Single quotes only when they avoid escaping.
- **Numbers:** Always include leading and trailing zero in floats (`0.5`, `13.0`). Use underscores for readability in large literals (`1_000_000`). Hex letters lowercase (`0xffff_f8f8`).

---

## Naming Conventions

| Type          | Convention     | Example                        |
|---------------|----------------|--------------------------------|
| File names    | snake_case     | `yaml_parser.gd`               |
| Class names   | PascalCase     | `class_name YAMLParser`        |
| Node names    | PascalCase     | `Player`, `Camera3D`           |
| Functions     | snake_case     | `func load_level():`           |
| Variables     | snake_case     | `var particle_effect`          |
| Signals       | snake_case     | `signal door_opened`           |
| Constants     | CONSTANT_CASE  | `const MAX_SPEED = 200`        |
| Enum names    | PascalCase     | `enum Element`                 |
| Enum members  | CONSTANT_CASE  | `EARTH`, `WATER`, `FIRE`       |

- **Private** functions and variables get a leading underscore: `_counter`, `_recalculate_path()`.
- File names mirror the `class_name` in snake_case: `class_name YAMLParser` → `yaml_parser.gd`.

---

## Code Order

Organize every script file in this order:

```
01. @tool, @icon, @static_unload       (if needed)
02. class_name
03. extends
04. ## doc comment

05. signals
06. enums
07. constants
08. static variables
09. @export variables
10. regular variables
11. @onready variables

12. _static_init()
13. static methods
14. built-in virtual methods (_init, _enter_tree, _ready, _process, _physics_process, …)
15. overridden custom methods
16. remaining public methods
17. private methods (prefixed _)
18. inner classes
```

---

## Type Hints

- Use static types wherever the type is known: `var speed: float = 200.0`.
- Use inferred types (`:=`) when the right-hand side makes the type obvious: `var name := "Player"`.
- Always type function parameters and return values in non-trivial functions:
  ```gdscript
  func take_damage(amount: int) -> void:
      health -= amount
  ```
- Avoid untyped `var` in public API — it hurts auto-complete and static analysis.

---

## Comments & Documentation

- Regular comments start with `# ` (space after `#`).
- Disabled/commented-out code uses `#` without a space: `#print("debug")`.
- Documentation comments use `## ` (double hash + space) and appear directly above the symbol.
- Prefer block comments on their own line over inline comments. Short inline comments (a few words) are fine.
- Use `#region` / `#endregion` to fold large sections — these must not have a leading space.

---

## Signals

```gdscript
# Define at top of file, past tense, snake_case
signal health_changed(new_value: int)
signal enemy_died

# Connect in _ready()
func _ready() -> void:
    health_changed.connect(_on_health_changed)

# Handler naming convention: _on_<NodeName>_<signal_name>
func _on_health_changed(new_value: int) -> void:
    update_health_bar(new_value)
```

---

## Export & OnReady

```gdscript
@export var speed: float = 300.0
@export var max_health: int = 100

@onready var sprite: Sprite2D = $Sprite2D
@onready var collision: CollisionShape2D = $CollisionShape2D
```

- Use `@export` instead of hardcoding tunable values.
- Use `@onready` to cache child node references — never call `get_node()` repeatedly in `_process`.

---

## Common Patterns

### Autoload (Singletons)
Use autoloads for truly global state (game settings, event bus, score manager). Keep them minimal.

### State Machines
Represent states as an `enum` and switch in `_physics_process` or `_process`. Extract complex states into inner classes or sub-scenes.

### Resource-based Data
Use `Resource` subclasses for data definitions (items, abilities, level config). Export them via `@export` for easy editor assignment.

```gdscript
class_name ItemData
extends Resource

@export var item_name: String = ""
@export var damage: int = 0
@export var icon: Texture2D
```

### Error Handling
- Check return values from methods that can fail (file I/O, networking).
- Use `assert()` for invariants during development.
- Use `push_error()` / `push_warning()` for non-fatal issues instead of bare `print()`.

---

## Anti-Patterns to Avoid

- **Don't use `get_node()` strings for deeply nested paths** — use `@onready` or typed scene references.
- **Don't call `get_parent()` chains** — use signals or dependency injection instead.
- **Don't use `setget` (Godot 3 syntax)** — use the `set` / `get` property block syntax in Godot 4.
- **Don't mix tabs and spaces** — the editor enforces tabs; keep it consistent.
- **Don't put game logic in `_init()`** — use `_ready()` once the node is in the scene tree.

---

## References
- [GDScript Basics](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_basics.html)
- [GDScript Style Guide](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_styleguide.html)
- [GDScript — More Efficiently](https://docs.godotengine.org/en/stable/tutorials/scripting/gdscript/gdscript_more_efficiently.html)
