\# NadeHelper (CounterStrikeSharp Plugin)

NadeHelper is a CounterStrikeSharp plugin for \*\*CS2\*\* that lets you:

\- Preview accurate jump-throw and W-jump grenade lineups in practice servers.

\- Simulate mid-air states with zero-gravity freeze for precise trajectory previews.

\- Adjust velocities and positions dynamically via commands for calibration.

\- Handle unintentional grenade throws during simulation by restoring the item.

Author: `gabeazar`

Plugin name: `NadeHelper`

---

\## Features

\- \*\*Hold-Reload Simulation\*\*: While on ground and holding Reload (R) with a grenade equipped, latch your position. Continue holding to teleport to a height offset, apply custom velocities (vertical for jump, horizontal/lateral for movement), force mid-air state, and set zero gravity to freeze without falling—allowing accurate grenade preview trajectories (e.g., via `sv\_grenade\_trajectory\_prac\_pipreview 1`).

\- \*\*Velocity \& Position Tuning\*\*: Live-adjust jump height offset, vertical/horizontal/lateral velocities, and forward nudge for realistic simulations (e.g., vertical ~301 for jump start, 0 for apex).

\- \*\*Air Simulation Toggle\*\*: Enable/disable mid-air state and zero-gravity freeze to compare behaviors.

\- \*\*Unintentional Throw Handling\*\*: If a throw occurs during freeze (e.g., due to teleport interrupting +attack), automatically restore the grenade and notify via chat.

\- \*\*Dump Command\*\*: Output current position and velocity for calibration.

\- \*\*Configurable\*\*: All parameters saved in a JSON config for persistence across restarts.

---

\## Requirements

\- \*\*CounterStrikeSharp\*\* (latest stable, v1.0.347+ recommended)

\- \*\*A running CS2 server\*\* (latest update)

\- \*\*Practice Mode\*\*: Best used with `sv\_cheats 1` and grenade preview convars like `sv\_grenade\_trajectory\_prac\_pipreview 1`.

---

\## Installation

1\. \*\*Place plugin\*\*:

&nbsp;  - Place the `NadeHelper.dll` into:

&nbsp;    ```

&nbsp;    csgo/addons/counterstrikesharp/plugins/NadeHelper/

&nbsp;    ```

&nbsp;  - Final path example:

&nbsp;    ```

&nbsp;    csgo/addons/counterstrikesharp/plugins/NadeHelper/NadeHelper.dll

&nbsp;    ```

2\. \*\*Configuration file\*\*:

&nbsp;  - On first run, CounterStrikeSharp will generate:

&nbsp;    ```

&nbsp;    csgo/addons/counterstrikesharp/configs/plugins/NadeHelper/NadeHelper.json

&nbsp;    ```

&nbsp;  - Edit this file to change options (see \*\*Configuration\*\* below).

3\. \*\*Restart the server\*\* (or reload CounterStrikeSharp) for changes to apply.

---

\## Configuration

\*\*Default config (example):\*\*

```jsonc

{

&nbsp; "Version": 2,

&nbsp; "JumpHeightOffset": 56.0,

&nbsp; "VerticalVelocity": 0.0,

&nbsp; "HorizontalVelocity": 0.0,

&nbsp; "LateralVelocity": 0.0,

&nbsp; "ForwardNudge": 0.0,

&nbsp; "AirSimulation": true

}

```

\*\*Options:\*\*

\- `JumpHeightOffset` (float, default: 56.0): Height adjustment in units for jump peak simulation.

\- `VerticalVelocity` (float, default: 0.0): Z-velocity (0 for apex, ~301 for jump start).

\- `HorizontalVelocity` (float, default: 0.0): Forward/back speed (influenced by W/S keys during hold).

\- `LateralVelocity` (float, default: 0.0): Left/right strafe speed (influenced by A/D keys during hold).

\- `ForwardNudge` (float, default: 0.0): Forward position adjustment in units.

\- `AirSimulation` (bool, default: true): Enable mid-air state and zero-gravity freeze.

---

\## Usage

\### Commands

All commands are console-based and prefixed with `css\_` for CSS compatibility. Use them to tune parameters live:

\- `css\_jumpoffset <value>`: Set jump height offset (e.g., `css\_jumpoffset 56`).

\- `css\_velz <value>`: Set vertical velocity (e.g., `css\_velz 301` for jump start).

\- `css\_velh <value>`: Set horizontal velocity for W/S movement.

\- `css\_vell <value>`: Set lateral velocity for A/D strafing.

\- `css\_wnudge <value>`: Set forward position nudge.

\- `css\_jumpairtoggle`: Toggle air simulation (mid-air + zero gravity) on/off.

\- `css\_jumpnade\_dump`: Dump your current position and velocity to chat for calibration.

\### In-Game Flow

1\. \*\*Equip a Grenade\*\*: Switch to any grenade (HE, Flash, Smoke, Molly, Incendiary, Decoy).

2\. \*\*Latch Position\*\*: Stand on ground and press/hold Reload (R)—this latches your position with forward nudge and height offset.

3\. \*\*Simulate Jump\*\*: While holding R, the plugin teleports you to the offset position, applies velocities (modified by held W/A/S/D keys), and (if enabled) forces mid-air state with zero gravity to freeze you.

&nbsp;  - Hold +attack (mouse1) to see the grenade preview trajectory in this simulated state.

&nbsp;  - Adjust view angles freely while frozen to preview different aims.

4\. \*\*Release \& Restore\*\*: Release R to restore normal gravity and ground state— you'll fall naturally if mid-air.

5\. \*\*Handle Throws\*\*: If an unintentional throw happens during freeze, the grenade is restored automatically with a chat message.

6\. \*\*Calibration Tips\*\*: Use `css\_jumpnade\_dump` while jumping/moving to capture real velocities, then set via commands (e.g., vertical ~301 at jump start). Test with different values for accuracy matching real jump-throws.

---

\## Troubleshooting

\- \*\*No Effect on Hold-R\*\*: Ensure a grenade is active, you're on ground, and the plugin is loaded (`css\_plugins list`). Check console for errors.

\- \*\*Player Falls or Trajectory Inaccurate\*\*: Toggle `css\_jumpairtoggle` off to disable freeze—compare behaviors. Adjust velocities to match real jumps (use dump command).

\- \*\*Unintentional Throws Not Restored\*\*: Verify you're using the latest plugin version; throws during teleport are detected via event hooks.

\- \*\*Config Not Saving\*\*: Ensure the config path is writable; restart server after edits.

