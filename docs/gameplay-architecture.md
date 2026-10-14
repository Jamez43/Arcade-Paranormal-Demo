# Gameplay Architecture

```mermaid
flowchart TD
    Input[Unity Input System\nvirtual joystick] --> Movement[PlayerMovement]
    Movement --> Player[Player controller]
    Player --> Stats[PlayerRuntimeStats]
    Player --> Combat{Attack type}
    Combat --> Melee[MeleeWeaponController]
    Combat --> Projectile[ProjectileWeaponController]
    Projectile --> Targeting[Physics2D overlap query\nnearest target in aim cone]
    Melee --> Enemies[Enemy controllers]
    Targeting --> Enemies
    Enemies --> Drops[XP pickup]
    Drops --> XP[XP and level controller]
    XP --> Upgrade[Upgrade-selection UI]
    Upgrade --> Stats
    WaveDirector[Adaptive wave director] --> Enemies
    Stats --> HUD[Health, cooldown, XP HUD]
    XP --> HUD
```

## State boundaries

| Boundary | Representation | Reason |
| --- | --- | --- |
| Authored balance data | `PlayerStats` and enemy-stat ScriptableObjects | Designers can tune base values in the Unity Editor. |
| Run-specific player state | `PlayerRuntimeStats` | Upgrade choices do not mutate source assets. |
| Reusable scene objects | Prefabs plus inactive pools | Reuse reduces allocation during combat-heavy play. |
| Wave composition | Candidate objects generated from budget and enemy definitions | Wave composition can respond to player performance. |

## Combat

The player can use melee or projectile weapons through separate controllers that read the same runtime-stat data. Projectile attacks query nearby colliders on the enemy layer, reject targets outside the aim cone, and choose the nearest remaining target. If no enemy qualifies, the projectile fires along the current aim direction.

## Navigation and spawning

Enemies navigate the 2D arena around obstacles. Before a spawn is accepted, the spawner checks candidate positions against the playable floor, walls, configured obstacle layers, and a clearance radius. This avoids spawning enemies into blocked geometry or on top of nearby colliders.
