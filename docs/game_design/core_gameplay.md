# Core Gameplay (TO REFINE)

## World Exploration

Players navigate a 3D world and interact with the environment.

Features:

* free movement in exploration areas
* towns and NPC interactions
* environmental exploration
* combat encounters

Encounters may be:

* random
* scripted
* story-driven

---

## Combat Encounters

When an encounter is triggered:

```
Exploration Scene
      ↓
Combat Scene Transition
      ↓
Grid Initialization
      ↓
Character Placement
```

Combat occurs in **dedicated tactical arenas**.

---

## Turn-Based Combat

Combat follows an initiative system.

```
Turn Start
   ↓
Player Turn
   ↓
Enemy Turn
   ↓
Turn End
```

Each character can perform actions such as:

* Move
* Attack
* Ability
* Item
* Wait / Defend

---

## Victory and Rewards

When all enemies are defeated:

* player receives rewards
* experience points
* items or equipment
* story progression

The game then returns to **exploration mode**.
