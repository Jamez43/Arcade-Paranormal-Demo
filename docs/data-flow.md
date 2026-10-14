# Data Flow

```mermaid
flowchart LR
    PlayerAsset[PlayerStats\nScriptableObject] -->|copy at run start| Runtime[PlayerRuntimeStats]
    EnemyAssets[EnemyStats\nScriptableObjects] --> Definitions[Enemy spawn definitions]
    Runtime --> Weapons[Weapon controllers]
    Runtime --> UpgradeButtons[Upgrade buttons]
    UpgradeButtons -->|modify only| Runtime
    Definitions --> WaveGen[Wave generator]
    WaveGen --> Candidates[Affordable wave candidates]
    Candidates --> Scorer[Fitness scorer]
    PlayerSignals[Health, kills/min,\nrecent damage, enemy count] --> Scorer
    Scorer --> Winner[Selected wave]
    Winner --> Pool[Enemy pool / instantiate]
```

## Why split base and runtime player data?

The base `PlayerStats` asset holds values shared by a character configuration: maximum health, damage, speed, cooldown, defense, pickup range, and attack type. A `PlayerRuntimeStats` instance begins as a copy and becomes the current run's mutable state.

That separation means a damage or cooldown upgrade has an immediate effect without permanently changing the editor-authored character asset. It also gives the game a clear reset point when a new run begins.

## Object lifecycle

```mermaid
stateDiagram-v2
    [*] --> InactivePool
    InactivePool --> Active: Wave / attack / XP drop
    Active --> InactivePool: Enemy defeated or despawned\nProjectile leaves camera bounds\nPickup consumed
    Active --> Destroyed: Scene unload
```

Pools are used before creating a new object. Each system keeps an inactive collection; when work is needed, it reactivates a compatible object if available and otherwise instantiates a prefab.
