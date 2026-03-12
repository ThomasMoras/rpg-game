# Combat System

Combat is a **turn-based tactical system** played on a grid.

---

## Combat Flow

```
World Exploration
      ↓
Encounter Trigger
      ↓
Scene Transition
      ↓
Combat Arena
      ↓
Grid Initialization
      ↓
Turn Order Calculation
```

---

## Combat State Machine

```
COMBAT_INIT
    ↓
TURN_START
    ↓
PLAYER_PLANNING
    ↓
PLAYER_TARGETING
    ↓
ACTION_EXECUTION
    ↓
ENEMY_TURN
    ↓
TURN_END
```

Loop continues until **victory or defeat**.

---

## Player Actions

During a turn the player may:

* Move
* Attack
* Use Ability
* Use Item
* Wait / Defend

Actions may consume:

* action points
* cooldowns
* resources

---

## Combat Mechanics

Core mechanics include:

* initiative-based turn order
* tactical positioning bonuses
* area-of-effect abilities
* status effects

Planned features:

* cover system
* advanced environmental interactions
