# Grid System

Combat takes place on a **3D tactical grid**.

---

## Grid Structure

The grid uses three spatial axes:

```
X → horizontal
Y → vertical height
Z → depth
```

This allows terrain variation and vertical gameplay.

---

## Grid Cells

Each tile is represented by a **GridCell node**.

Example structure:

```
GridCell (Node3D)
├── MeshInstance3D
├── CollisionShape3D
├── Highlight
└── Marker3D
```

---

## Grid Responsibilities

The grid system handles:

* grid generation
* cell coordinate mapping
* movement validation
* pathfinding
* line-of-sight checks
* cell occupation tracking

---

## Pathfinding

Movement uses an **A* pathfinding algorithm**.

The system calculates:

* shortest path
* movement cost
* obstacle avoidance

Terrain may influence movement cost.

---

## Tactical Feedback

The grid visually communicates tactical information.

Examples:

* movement range highlights
* attack range indicators
* path previews
* target markers
