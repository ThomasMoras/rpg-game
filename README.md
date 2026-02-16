# [Game Title]

> A tactical action RPG built with Godot Engine

[![Godot Engine](https://img.shields.io/badge/Godot-4.x-blue.svg)](https://godotengine.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

## 📖 About

[Brief description of your game - 2-3 sentences about the core gameplay experience, setting, or unique features]

## ✨ Features

- **Tactical Combat System** - [Description to add]
- **Character Progression** - [Description to add]
- **Dynamic Action Mechanics** - [Description to add]
- **Story-Driven Campaign** - [Description to add]

## 🎯 3D Grid Combat System

### Combat Flow

```
World Exploration → Encounter Trigger → Scene Transition → Combat Arena
         ↓
  Grid Initialization → Character Placement → Turn Order Calculation
         ↓
  PLAYER TURN: Planning Phase → Action Selection → Target Selection → Execution
         ↓
  ENEMY TURN: AI Decision → Movement → Action Execution
         ↓
  Victory/Defeat Check → Rewards Distribution → Return to World
```

### Grid Architecture

**Grid Cell Structure:**
```
GridCell (Node3D)
├── MeshInstance3D (visual representation)
├── CollisionShape3D (mouse picking/raycasting)
├── Highlight (MeshInstance3D - selection indicator)
└── Marker3D (character placement point)
```

**Grid Manager Responsibilities:**
- Grid generation and initialization
- Cell coordinate mapping (Vector2i ↔ Vector3)
- Pathfinding (A* algorithm)
- Movement range calculation
- Line of sight calculations
- Occupied cell tracking

**Combat Character Structure:**
```
CharacterCombat (CharacterBody3D)
├── Model (imported 3D model)
├── AnimationPlayer
├── HealthBar3D (billboard UI)
├── SelectionIndicator
├── StatsComponent (custom script)
└── AbilityController
```

### Autoload/Singleton Systems

Configure these in **Project Settings → Autoload**:

| Autoload Name | Script Path | Purpose |
|---------------|-------------|---------|
| GameManager | `scripts/autoload/game_manager.gd` | Overall game state, scene management |
| CombatManager | `scripts/autoload/combat_manager.gd` | Combat state, turn order, initiative |
| EventBus | `scripts/autoload/event_bus.gd` | Global event system for decoupled communication |
| SceneTransition | `scripts/autoload/scene_transition.gd` | Smooth transitions between world ↔ combat |
| AudioManager | `scripts/autoload/audio_manager.gd` | Music and SFX management |
| SaveManager | `scripts/autoload/save_manager.gd` | Save/load game state persistence |

### Combat State Machine

```
COMBAT_INIT          → Initialize grid, place characters
  ↓
TURN_START           → Calculate turn order, apply start-of-turn effects
  ↓
PLAYER_PLANNING      → Player selects action (Move/Attack/Ability/Item/Wait)
  ↓
PLAYER_TARGETING     → Select target/destination on grid
  ↓
ACTION_EXECUTION     → Perform action with animation
  ↓
ENEMY_TURN           → AI processes and executes actions
  ↓
TURN_END             → Apply end-of-turn effects, check win/lose conditions
  ↓
(Loop back to TURN_START or proceed to COMBAT_END)
```

### Key Technical Systems

**Pathfinding System:**
- A* algorithm for grid-based movement
- Movement cost calculation (terrain, obstacles)
- Range highlighting (movement range, attack range)
- Path visualization

**Targeting System:**
- Single target selection
- Area of Effect (AoE) targeting
- Line/Cone targeting
- Friendly fire detection
- Range validation

**Grid Coordinates:**
```gdscript
# Grid position (2D integer coordinates)
var grid_pos: Vector2i = Vector2i(5, 3)

# Convert to world position (3D)
var world_pos: Vector3 = grid_to_world(grid_pos)

# Convert world position back to grid
var grid_pos_back: Vector2i = world_to_grid(world_pos)
```

### Visual Feedback Systems

- **Movement Preview** - Show valid movement cells
- **Attack Range** - Highlight cells within attack range
- **Path Display** - Show movement path on grid
- **Damage Numbers** - Floating combat text
- **Status Indicators** - Visual effects for buffs/debuffs
- **Turn Order UI** - Display initiative order

## 🎮 Gameplay

### World Exploration
- **Free Movement** - Navigate the 3D world map
- **Encounter System** - Random and scripted combat encounters
- **NPC Interaction** - Towns, shops, and quest givers
- **Environmental Exploration** - Discover secrets and loot
- [Additional features to be completed]

### Tactical Combat System

**Grid-Based Movement:**
- Turn-based tactical positioning on a 3D grid
- Movement range based on character stats
- Terrain affects movement cost
- Height advantages and line of sight

**Action System:**
- **Move** - Reposition on the grid
- **Attack** - Basic melee or ranged attacks
- **Abilities** - Special skills with cooldowns/resources
- **Items** - Use consumables and equipment
- **Wait/Defend** - End turn early or take defensive stance

**Combat Mechanics:**
- Initiative-based turn order
- Flanking and positioning bonuses
- Area of Effect abilities
- Status effects and buffs/debuffs
- Cover system (planned)
- Environmental interactions (planned)

### Character Customization
[To be completed - explain character builds, skills, equipment]

### Progression
[To be completed - detail leveling, abilities, unlocks]

## 🛠️ Built With

- **Engine:** Godot 4.x
- **Language:** GDScript
- **Version Control:** Git

## 📋 Requirements

- Godot Engine 4.x or higher
- [Any additional dependencies]

## 🚀 Getting Started

### Installation

1. Clone the repository
```bash
git clone [your-repository-url]
cd [project-folder]
```

2. Open the project in Godot
```bash
godot --editor .
```

3. Press F5 to run the game

### Controls

[To be completed]

| Action | Key/Button |
|--------|------------|
| Move | WASD / Arrow Keys |
| Attack | [To add] |
| Special Ability | [To add] |
| Pause | ESC |

## 🗺️ Roadmap

### Current Version (v0.1.0)
- [ ] Basic movement system
- [ ] Combat mechanics prototype
- [ ] Character controller
- [ ] UI framework

### Upcoming Features
- [ ] Complete tactical combat system
- [ ] Character skill trees
- [ ] Enemy AI behaviors
- [ ] Inventory system
- [ ] Save/Load functionality
- [ ] Multiple playable characters
- [ ] Story missions and quests
- [ ] Boss encounters
- [ ] Sound effects and music
- [ ] Dialogue system

### Future Considerations
- [ ] Co-op multiplayer
- [ ] Procedural generation
- [ ] New game+ mode
- [ ] Achievement system

## 📁 Project Structure

```
project_root/
├── .git/
├── .gitignore
├── README.md
├── project.godot
│
├── assets/                      # Raw assets
│   ├── models/
│   │   ├── characters/
│   │   │   ├── player/
│   │   │   └── enemies/
│   │   ├── environments/
│   │   │   ├── terrain/
│   │   │   ├── buildings/
│   │   │   └── props/
│   │   └── vfx/
│   ├── textures/
│   │   ├── characters/
│   │   ├── environments/
│   │   ├── ui/
│   │   └── grid/
│   ├── materials/
│   ├── animations/
│   ├── audio/
│   │   ├── music/
│   │   ├── sfx/
│   │   └── ambient/
│   ├── fonts/
│   └── shaders/
│
├── scenes/                      # All .tscn files
│   ├── main.tscn               # Entry point
│   │
│   ├── world/                  # Overworld/exploration
│   │   ├── world_map.tscn
│   │   ├── regions/
│   │   │   ├── region_01.tscn
│   │   │   ├── region_02.tscn
│   │   │   └── town_01.tscn
│   │   ├── navigation/
│   │   │   ├── nav_mesh.tscn
│   │   │   └── waypoints/
│   │   └── encounters/
│   │       └── encounter_trigger.tscn
│   │
│   ├── combat/                 # Tactical combat
│   │   ├── battle_arena.tscn
│   │   ├── grid/
│   │   │   ├── grid_system.tscn
│   │   │   ├── grid_cell.tscn
│   │   │   ├── grid_cursor.tscn
│   │   │   └── movement_preview.tscn
│   │   ├── abilities/
│   │   │   ├── melee_attack.tscn
│   │   │   ├── ranged_attack.tscn
│   │   │   ├── aoe_ability.tscn
│   │   │   └── buff_effect.tscn
│   │   ├── vfx/
│   │   │   ├── hit_effect.tscn
│   │   │   ├── damage_numbers.tscn
│   │   │   └── status_indicators.tscn
│   │   └── ui/
│   │       ├── turn_order.tscn
│   │       ├── action_menu.tscn
│   │       └── target_selector.tscn
│   │
│   ├── characters/
│   │   ├── player/
│   │   │   ├── player_world.tscn      # Overworld version
│   │   │   ├── player_combat.tscn     # Combat version
│   │   │   └── player_classes/
│   │   └── enemies/
│   │       ├── enemy_base_combat.tscn
│   │       └── enemy_types/
│   │           ├── grunt.tscn
│   │           ├── archer.tscn
│   │           └── boss.tscn
│   │
│   ├── camera/
│   │   ├── world_camera.tscn
│   │   ├── combat_camera.tscn
│   │   └── camera_controller.tscn
│   │
│   ├── ui/
│   │   ├── menus/
│   │   │   ├── main_menu.tscn
│   │   │   ├── pause_menu.tscn
│   │   │   └── settings_menu.tscn
│   │   ├── hud/
│   │   │   ├── world_hud.tscn
│   │   │   ├── combat_hud.tscn
│   │   │   ├── health_bar_3d.tscn     # Billboard HP bars
│   │   │   └── minimap.tscn
│   │   ├── inventory/
│   │   │   ├── inventory_ui.tscn
│   │   │   └── equipment_ui.tscn
│   │   └── dialogs/
│   │       └── dialog_box.tscn
│   │
│   └── environments/
│       ├── lighting/
│       │   ├── directional_light.tscn
│       │   └── environment.tscn
│       └── skybox/
│
├── scripts/                     # All .gd files
│   ├── autoload/
│   │   ├── game_manager.gd
│   │   ├── event_bus.gd
│   │   ├── audio_manager.gd
│   │   ├── save_manager.gd
│   │   ├── combat_manager.gd
│   │   └── scene_transition.gd
│   │
│   ├── world/
│   │   ├── world_controller.gd
│   │   ├── player_movement.gd         # Overworld movement
│   │   ├── encounter_manager.gd
│   │   └── npc_controller.gd
│   │
│   ├── combat/
│   │   ├── grid/
│   │   │   ├── grid_manager.gd
│   │   │   ├── grid_cell.gd
│   │   │   ├── pathfinding.gd         # A* for grid movement
│   │   │   └── grid_highlighter.gd
│   │   ├── turn_system.gd
│   │   ├── action_system.gd
│   │   ├── targeting_system.gd
│   │   ├── ability_executor.gd
│   │   └── combat_camera_controller.gd
│   │
│   ├── characters/
│   │   ├── character_base.gd
│   │   ├── player/
│   │   │   ├── player_controller_world.gd
│   │   │   └── player_controller_combat.gd
│   │   └── enemies/
│   │       ├── enemy_base.gd
│   │       └── ai/
│   │           ├── ai_controller.gd
│   │           ├── ai_tactical.gd     # Grid-based AI
│   │           └── behavior_tree.gd
│   │
│   ├── camera/
│   │   ├── camera_3d_controller.gd
│   │   ├── orbit_camera.gd
│   │   └── camera_shake.gd
│   │
│   ├── systems/
│   │   ├── inventory_system.gd
│   │   ├── equipment_system.gd
│   │   ├── stat_system.gd
│   │   ├── progression_system.gd
│   │   ├── quest_system.gd
│   │   └── dialog_system.gd
│   │
│   ├── ui/
│   │   ├── menu_controller.gd
│   │   ├── hud_controller.gd
│   │   └── inventory_ui_controller.gd
│   │
│   └── utils/
│       ├── constants.gd
│       ├── grid_utils.gd              # Grid coordinate helpers
│       ├── math_utils.gd
│       └── debug_draw.gd              # Debug visualization
│
├── resources/                   # All .tres files
│   ├── characters/
│   │   ├── stats/
│   │   │   ├── character_stats.tres   # Custom Resource
│   │   │   └── enemy_stats/
│   │   ├── abilities/
│   │   │   ├── ability_base.tres
│   │   │   └── skills/
│   │   └── classes/
│   │       ├── warrior.tres
│   │       ├── mage.tres
│   │       └── ranger.tres
│   │
│   ├── combat/
│   │   ├── grid_configs/
│   │   │   ├── standard_grid.tres     # Grid size, spacing
│   │   │   └── large_grid.tres
│   │   ├── battle_formations/
│   │   └── encounter_data/
│   │
│   ├── items/
│   │   ├── weapons/
│   │   ├── armor/
│   │   └── consumables/
│   │
│   ├── world/
│   │   ├── region_data/
│   │   └── encounter_tables/
│   │
│   └── themes/
│       └── main_theme.tres
│
├── data/                        # JSON, CSV, etc.
│   ├── dialogs/
│   ├── quests/
│   ├── localization/
│   └── balance/                 # Game balance spreadsheets
│       ├── damage_formulas.json
│       └── character_stats.csv
│
└── addons/
    └── [plugins]/
```

### Structure Principles

**Separation by Function:**
- `scenes/` - Visual structure and scene composition
- `scripts/` - Logic and behavior
- `resources/` - Reusable data containers (.tres files)
- `assets/` - Raw media files (models, textures, audio)

**Dual Mode Architecture:**
- `world/` - Overworld exploration and navigation
- `combat/` - 3D grid-based tactical combat
- Characters have separate versions for each mode

## 💻 Implementation Notes

### Grid System Setup

**Creating the Grid:**
```gdscript
# scripts/combat/grid/grid_manager.gd
class_name GridManager
extends Node3D

@export var grid_size: Vector2i = Vector2i(10, 10)
@export var cell_size: float = 1.0
@export var cell_height: float = 0.5

var cells: Dictionary = {}  # Vector2i -> GridCell
var occupied_cells: Dictionary = {}  # Vector2i -> Character

func grid_to_world(grid_pos: Vector2i) -> Vector3:
    return Vector3(
        grid_pos.x * cell_size,
        0,
        grid_pos.y * cell_size
    )

func world_to_grid(world_pos: Vector3) -> Vector2i:
    return Vector2i(
        int(world_pos.x / cell_size),
        int(world_pos.z / cell_size)
    )
```

**Grid Cell Highlighting:**
- Use MeshInstance3D with ShaderMaterial for smooth highlighting
- Animate highlights with tweens
- Different colors for: movement, attack, AoE, invalid

**Camera Control:**
- Orbit camera for combat (mouse drag to rotate)
- Zoom in/out with mouse wheel
- Tilt and pan controls
- Auto-focus on active character

### Data-Driven Design

**Custom Resources for Abilities:**
```gdscript
# Create ability_data.gd as a Resource
class_name AbilityData
extends Resource

@export var ability_name: String
@export var ability_type: String  # "melee", "ranged", "aoe", "buff"
@export var range: int
@export var damage: int
@export var area_size: int  # For AoE
@export var cooldown: int
@export var animation_name: String
```

**Character Stats Resource:**
```gdscript
class_name CharacterStats
extends Resource

@export var max_health: int
@export var movement_range: int
@export var initiative: int
@export var attack_power: int
@export var defense: int
```

### AI System

**Tactical AI Considerations:**
- Evaluate movement positions based on:
  - Distance to enemies
  - Cover availability
  - Ability range optimization
  - Group formation
- Action priority system
- Difficulty scaling through decision weights

### Performance Tips

- Use object pooling for damage numbers and VFX
- Implement frustum culling for large grids
- LOD (Level of Detail) for character models
- Batch similar visual effects
- Cache pathfinding results when possible


## 📝 Development Notes

### Code Style
[To be completed - add your coding conventions]

### Testing
[To be completed - describe testing approach]

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👥 Authors

- **[Your Name]** - *Initial work* - [Your GitHub](https://github.com/yourusername)

## 🙏 Acknowledgments

[To be completed - credit assets, tutorials, inspiration, etc.]

## 📞 Contact

- Project Link: [https://github.com/yourusername/projectname](https://github.com/yourusername/projectname)
- Email: [your.email@example.com]

---

**Status:** 🚧 In Development

*Last Updated: [Current Date]*
