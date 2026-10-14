# Arcade Paranormal — Technical Demo

[Play the browser build on itch.io](https://jamez43.itch.io/arcade-paranormal)

Arcade Paranormal is a mobile-first 2D survival roguelite built in Unity and C#. The player fights possessed arcade machines, collects XP, chooses run-specific upgrades, and survives adaptive enemy waves in a haunted retro arcade.

This repository is a recruiter-facing technical walkthrough. It intentionally contains no production source code, game assets, or build files.

## What I built

- Mobile virtual-joystick movement using Unity's Input System.
- Melee and projectile combat systems, including cone-based nearest-target selection for projectile attacks.
- Three enemy archetypes with distinct ScriptableObject configuration and attack behavior.
- Object pooling for enemies, XP pickups, and projectiles to reduce runtime allocation during play.
- Obstacle-aware enemy spawning and 2D navigation around level geometry.
- XP progression that pauses the run for one of six upgrades affecting damage, defense, movement speed, cooldown, or pickup range.
- An adaptive wave director that generates and ranks budget-constrained wave candidates from player-performance signals.
- Menu, character-selection, HUD, pause, and game-over flows.

## Technical focus

### Data-driven gameplay

Player and enemy base attributes are authored as ScriptableObjects. At the beginning of a run, the selected player configuration is copied into a mutable runtime-stat object. Upgrade selections only change that runtime copy, so tuning source data in the editor remains separate from per-run state.

See [gameplay architecture](docs/gameplay-architecture.md) and [data flow](docs/data-flow.md).

### Adaptive wave generation

The wave director turns available enemy definitions and a wave budget into multiple affordable candidate waves. It scores each candidate for difficulty fit, enemy variety, and pressure. The target difficulty moves in response to the player's health, recent damage, kill rate, and current enemy count, then the highest-scoring candidate is spawned.

See [adaptive wave director](docs/adaptive-wave-director.md).

### Runtime performance decisions

Enemy, projectile, and XP systems reuse inactive objects before instantiating new ones. Projectile targeting uses Unity 2D physics overlap queries limited to an enemy layer and selects the nearest target within an aim cone. Spawn locations are validated against tilemaps, obstacle layers, and nearby colliders before an enemy is placed.

## Stack

Unity `6000.2.8f1` · C# · Unity Input System · Unity 2D Navigation · ScriptableObjects · Unity Test Framework

## Play and contact

- [Play Arcade Paranormal](https://jamez43.itch.io/arcade-paranormal)
- [GitHub profile](https://github.com/Jamez43)
- [LinkedIn](https://www.linkedin.com/in/james-wyllie)

## Repository scope

The complete Unity project remains private while the game is in active development. This public companion repository is designed to make the architecture, engineering decisions, and playable build easy to evaluate.
