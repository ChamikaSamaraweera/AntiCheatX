# AntiCheatX Documentation

## 📑 Table of Contents
1. [Introduction](#introduction)
2. [Installation](#installation)
3. [Commands](#commands)
4. [Permissions](#permissions)
5. [Configuration](#configuration)
6. [Detection Methods](#detection-methods)
7. [Punishment System](#punishment-system)
8. [Performance & Optimization](#performance--optimization)
9. [Logging & Troubleshooting](#logging--troubleshooting)

---

## 1. Introduction <a name="introduction"></a>
AntiCheatX is a high-performance, lightweight anti-cheat solution specifically designed for Paper Minecraft servers (version 1.21.1). It utilizes advanced heuristic analysis and physics validation to detect and prevent common cheats while minimizing server impact.

---

## 2. Installation <a name="installation"></a>
1. **Requirements**:
   - Paper 1.21.1 or compatible fork.
   - Java 21 or higher.
2. **Setup**:
   - Download `AntiCheatX.jar`.
   - Place it in your server's `plugins/` directory.
   - Restart the server to generate default configuration files.
   - Configure the plugin in `plugins/AntiCheatX/config.yml`.
   - Use `/acx reload` to apply changes without restarting.

---

## 3. Commands <a name="commands"></a>
All commands require the `anticheatx.admin` permission.

| Command | Usage | Description |
|:---|:---|:---|
| `/acx` | `/acx` | Shows the help menu with all available commands. |
| `/acx reload` | `/acx reload` | Reloads the configuration and plugin settings. |
| `/acx status` | `/acx status` | Displays plugin status, version, and active thresholds. |
| `/acx violations` | `/acx violations <player>` | Shows detailed violation levels for a specific player. |
| `/acx alerts` | `/acx alerts` | Toggles on-screen violation alerts for the admin. |

---

## 4. Permissions <a name="permissions"></a>

| Permission | Default | Description |
|:---|:---|:---|
| `anticheatx.admin` | `op` | Full access to all AntiCheatX commands. |
| `anticheatx.alerts` | `op` | Allows receiving real-time alerts about suspicious players. |
| `anticheatx.bypass` | `false` | Players with this permission are ignored by all checks. |

---

## 5. Configuration <a name="configuration"></a>
The `config.yml` file is divided into several key sections:

### 5.1 General Settings
- `prefix`: The prefix used in all plugin messages.
- `welcomeMessage`: Settings for the protected server announcement on join.

### 5.2 Violation Thresholds
Defines how many violation points a player must accumulate before the configured punishment is applied.
- `violations.fly`: (Default: 10)
- `violations.speed`: (Default: 8)
- ... (and more for each check type)

### 5.3 Punishment Actions
Determines what happens when a threshold is reached.
- `actions`: Options are `NONE`, `WARN`, `KICK`, `BAN`.
- `banDuration`: Duration in minutes for temporary bans (0 for permanent).
- `resetAfterBan`: Whether to clear violations after a ban is applied.

---

## 6. Detection Methods <a name="detection-methods"></a>

### 6.1 Fly Detection
- **Logic**: Monitors air time and vertical movement.
- **Technical Detail**: If a player is not on the ground, in water, or using an Elytra for more than `maxAirTime` (default 1500ms) and continues to move upwards against gravity, it triggers a violation.

### 6.2 Speed Hacks
- **Logic**: Validates horizontal movement speed.
- **Technical Detail**: Calculates distance between ticks and compares it against a `baseMaxSpeed` (0.75 blocks/tick), adjusted for sprinting (1.3x) and Speed potion effects (0.2x per level).

### 6.3 Irregular Movement
- **Logic**: Detects impossible physics behaviors.
- **Technical Detail**:
  - **Angle Analysis**: Detects sharp direction changes (> 144°) while moving at high speeds (> 0.4 blocks/tick).
  - **Wall Climbing**: Detects upward movement (> 0.1 blocks/tick) while touching a solid block without jumping or climbing (vines/ladders).

### 6.4 X-Ray Detection
- **Logic**: Analyzes mining patterns and ore ratios.
- **Technical Detail**:
  - **Ratio Monitoring**: Tracks the ratio of valuable ores mined against total blocks. If the ratio exceeds `thresholdRatio` (default 10%) after a sample of 100 blocks, a violation is added.
  - **Direct Tunneling**: Analyzes vectors between the last 3 valuable ore locations. If the player tunnels in a straight line (angle < 45°) over distances > 3 blocks directly to ores, it flags a violation.

### 6.5 KillAura
- **Logic**: Monitors combat interactions.
- **Technical Detail**:
  - **Attack Rate**: Triggers if the interval between attacks is less than 120ms.
  - **Targeting Angle**: Analyzes the angle between the player's look direction and the target. If the angle is > 90° (attacking behind or to the side), it flags a violation.

### 6.6 NoFall
- **Logic**: Compares fall distance with damage received.
- **Technical Detail**: Calculates expected damage (`fallDistance - 3.0`). If the player receives less than 60% of the expected damage while falling, it triggers a violation.

### 6.7 Timer manipulation
- **Logic**: Detects client-side tick rate changes.
- **Technical Detail**: Measures the frequency of movement packets sent by the client. If the client sends significantly more packets (> 1.3x) than the server ticks in a 1-second interval, it flags a Timer violation.

### 6.8 Scaffold
- **Logic**: Analyzes block placement behavior.
- **Technical Detail**:
  - **Placement Speed**: Detects blocks placed with less than 100ms interval.
  - **Look Analysis**: Flags if a player is placing blocks in a straight line while looking more than 90° away from the placement location.

---

## 7. Punishment System <a name="punishment-system"></a>
AntiCheatX uses a tiered punishment system:
1. **Detection**: Each time a check fails, a violation point is added.
2. **Alerts**: Admins receive alerts every 3 violations or when the threshold is reached.
3. **Warning**: At 50% of the threshold, the player receives a warning message.
4. **Action**: Upon reaching 100% of the threshold, the configured action (`KICK`, `BAN`, etc.) is executed.

---

## 8. Performance & Optimization <a name="performance--optimization"></a>
- **Asynchronous Processing**: Movement checks are offloaded from the main tick loop where possible to prevent TPS drops.
- **Smart Sampling**: Data like mining ratios and movement history are sampled over time to reduce per-tick calculations.
- **Auto-Decay**: Violation levels automatically decay for inactive players every 5 minutes.

---

## 9. Logging & Troubleshooting <a name="logging--troubleshooting"></a>
- **Console**: Real-time violations are logged to the server console.
- **File Logging**: Detailed logs including timestamps and technical data are saved to `plugins/AntiCheatX/logs/violations.log`.
- **False Positives**: If players are being wrongly flagged, consider increasing the thresholds in `config.yml` or checking for server-side lag (TPS).

---
*Documentation generated for AntiCheatX v1.0.0*
