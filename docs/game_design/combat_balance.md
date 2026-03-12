# Combat Balance (TO REFINE)

This document defines the **core balance rules** for the combat system.

It ensures that characters, enemies, and abilities remain **consistent and scalable** during development.

Combat balance should be **data-driven** and implemented through resources or configuration files when possible.

---

# Core Combat Philosophy

Combat should emphasize:

* **positioning and tactical movement**
* **ability synergy**
* **environmental interactions**
* **clear player decision-making**

Encounters should feel:

* challenging but readable
* tactical rather than random
* fast-paced due to small battle arenas

---

# Core Character Stats

Each character is defined by a set of combat stats.

| Stat           | Description                                 |
| -------------- | ------------------------------------------- |
| Health         | Maximum life before defeat                  |
| Movement Range | Number of grid cells the character can move |
| Initiative     | Determines turn order                       |
| Attack Power   | Base damage modifier                        |
| Defense        | Reduces incoming damage                     |

Example baseline values:

| Character Type | Health | Move | Initiative | Attack | Defense |
| -------------- | ------ | ---- | ---------- | ------ | ------- |
| Player Hero    | 100    | 5    | 10         | 12     | 8       |
| Enemy Grunt    | 70     | 4    | 8          | 10     | 6       |
| Enemy Archer   | 60     | 4    | 12         | 11     | 5       |
| Boss           | 250    | 4    | 7          | 16     | 12      |

These values should be **adjusted during playtesting**.

---

# Damage Formula

Basic attack damage:

```
Damage = AttackPower - Defense
```

Minimum damage rule:

```
Damage >= 1
```

Future improvements may include:

* critical hits
* armor penetration
* elemental damage

Example expanded formula:

```
Damage = (AttackPower * AbilityMultiplier) - Defense
```

---

# Ability Balance Rules

Abilities should follow consistent design guidelines.

### Damage Abilities

| Type             | Typical Multiplier |
| ---------------- | ------------------ |
| Basic Attack     | x1.0               |
| Heavy Attack     | x1.5               |
| Area Attack      | x0.75              |
| Ultimate Ability | x2.0+              |

Area attacks deal **less damage** to compensate for multiple targets.

---

### Cooldowns

Typical cooldown ranges:

| Ability Type   | Cooldown  |
| -------------- | --------- |
| Basic Skill    | 0–1 turns |
| Strong Ability | 2–3 turns |
| Power Ability  | 4–5 turns |

Cooldowns prevent ability spamming.

---

# Movement Balance

Movement range strongly affects tactical options.

Recommended ranges:

| Character Type | Movement |
| -------------- | -------- |
| Heavy Units    | 3–4      |
| Standard Units | 4–5      |
| A              |          |
