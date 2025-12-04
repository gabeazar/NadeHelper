# NadeHelper (CounterStrikeSharp Plugin)

NadeHelper is a CounterStrikeSharp plugin for **CS2** that lets you:

- Preview **jump-throw** and **W-jump** grenade lineups in practice servers using the grenade pip.
- Simulate the player at the **exact jump apex** (position + velocity) for consistent preview.
- Tune apex offsets and velocities live via commands to match real jump-throws.
- Handle unintentional grenade throws during preview by restoring the grenade.

Author: `gabeazar`  
Plugin name: `NadeHelper`  
Version: `0.9`

---

## Features

- **Hold-Reload Apex Simulation**  
  While on the ground with a grenade equipped, holding **Reload (R)** latches your state and teleports you to a **synthetic jump apex** every tick:
  - Position offset: configurable forward/right/up offsets relative to where you latched.
  - Velocity: configurable forward/side/vertical velocity matching a real jump-throw.
  - Optional “air simulation”: forces off-ground flag for a mid-air look.
  The grenade pip (e.g. `sv_grenade_trajectory_prac_pipreview 1`) then shows the trajectory as if you released the grenade at that apex.

- **Debounced R Release**  
  To avoid flicker or packet-loss issues, R must be released for **3 consecutive ticks** before the preview unlatches. Brief input gaps won’t drop you out of preview.

- **W-Based Micro Nudge Only While Latched**  
  While latched and holding **R**, pressing **W** applies a small configurable forward nudge to your apex position. Releasing W (while still holding R) removes the nudge. Movement keys have **no effect** when not latched.

- **Apex Position & Velocity Tuning**  
  Live-adjust:
  - Apex height (`JumpHeightOffset`)
  - Forward/right offset (`ForwardOffset`, `RightOffset`)
  - Apex velocity (`VerticalVelocity`, `HorizontalVelocity`, `LateralVelocity`)
  - Forward nudge distance (`ForwardNudge`)
  so that the pip preview matches your real jump-throw landings.

- **Unintentional Throw Handling**  
  If you accidentally throw your grenade during preview (for example while adjusting), the plugin **gives you the same grenade back** on the next frame and notifies you in chat.

- **Dump Command for Calibration**  
  Quickly print your current position and velocity to chat to help calibrate offsets and velocities.

---

## Requirements

- **CounterStrikeSharp** (latest stable, v1.0.347+ recommended)  
- **CS2 Dedicated Server** (up-to-date)  
- **Practice Mode Recommended**:
  - `sv_cheats 1`
  - `sv_grenade_trajectory_prac_pipreview 1`
  - `sv_grenade_trajectory_prac_trailtime 10` (optional)
  - `sv_infinite_ammo 2` (optional)

---

## Installation

1. **Place plugin DLL**

   Copy `NadeHelper.dll` into:

   ```text
   csgo/addons/counterstrikesharp/plugins/NadeHelper/
   ```

   Example final path:

   ```text
   csgo/addons/counterstrikesharp/plugins/NadeHelper/NadeHelper.dll
   ```

2. **Start / restart server**

   - Start (or restart) your CS2 server.
   - CounterStrikeSharp will automatically load the plugin.
   - You can verify with:
     ```text
     css_plugins list
     ```

3. **Config file generation**

   On first run, CounterStrikeSharp will generate:

   ```text
   csgo/addons/counterstrikesharp/configs/plugins/NadeHelper/NadeHelper.json
   ```

   Edit this file to change persistent options (see **Configuration** below), then restart the server to apply.

---

## Configuration

**Example config (generated / simplified):**

```jsonc
{
  "Version": 3,
  "JumpHeightOffset": 56.0,
  "ForwardOffset": 90.0,
  "RightOffset": 0.0,
  "VerticalVelocity": 0.0,
  "HorizontalVelocity": 250.0,
  "LateralVelocity": 0.0,
  "ForwardNudge": 3.0,
  "AirSimulation": true
}
```

**Options:**

- `JumpHeightOffset` (float, default: `56.0`)  
  Height (Z) offset in units from your current ground position to the synthetic **jump apex**.

- `ForwardOffset` (float, default: `90.0`)  
  Forward offset (along your view direction) from latch position to apex. Adjust to match how far forward you’ve moved when you release the grenade in a real jump-throw.

- `RightOffset` (float, default: `0.0`)  
  Side offset (to the right of your view) from latch position to apex. Use for lineups that include A/D strafing.

- `VerticalVelocity` (float, default: `0.0`)  
  Vertical velocity at apex (units/sec). `0.0` ≈ exact apex; positive = slightly before apex, negative = slightly after.

- `HorizontalVelocity` (float, default: `250.0`)  
  Forward velocity at apex (units/sec). Around `250` is full run speed; reduce for walk/slow throws.

- `LateralVelocity` (float, default: `0.0`)  
  Sideways velocity at apex (units/sec). Use for jump-throws where you are still moving left/right.

- `ForwardNudge` (float, default: `3.0`)  
  Extra forward distance (units) applied when you hold **R + W** while latched. Acts as a micro-adjustment tool in preview.

- `AirSimulation` (bool, default: `true`)  
  When `true`, the plugin clears the ground flag each tick while latched so you appear mid-air; the position/velocity are still fully controlled via teleport and the values above.

---

## Usage

### Commands

Use these commands in the **server console** or in your own console (with appropriate permissions) to tune NadeHelper while in-game:

- `css_jumpoffset <value>`  
  Set apex height above your current ground position (e.g. `css_jumpoffset 56`).

- `css_offset_forward <value>`  
  Set forward apex offset (units along your current view, e.g. `css_offset_forward 90`).

- `css_offset_right <value>`  
  Set right apex offset (units to your view’s right, e.g. `css_offset_right 0`).

- `css_velz <value>`  
  Set vertical velocity at apex (e.g. `css_velz 0` for exact apex, slightly >0 or <0 to bias before/after).

- `css_velh <value>`  
  Set forward (view-direction) velocity at apex (e.g. `css_velh 250` for full run speed).

- `css_vell <value>`  
  Set lateral (sideways) velocity at apex (e.g. `css_vell 0` unless you strafe).

- `css_wnudge <value>`  
  Set forward nudge distance applied while **R + W** are held during preview (e.g. `css_wnudge 3`).

- `css_jumpairtoggle`  
  Toggle “air simulation” on/off (controls whether the plugin forces you off-ground visually while latched).

- `css_jumpnade_dump`  
  Print your current position and velocity to chat (useful for measuring real jump apex velocities to copy into config).

---

### In-Game Flow (Jump-Throw Pip Preview)

1. **Equip a grenade**  
   Use any grenade: HE, Flash, Smoke, Molotov, Incendiary, Decoy.

2. **Latch into preview**  
   - Stand on the ground.  
   - Hold **Reload (R)** with the grenade out.  
   - The plugin latches your state and starts teleporting you every tick to the configured **apex position** with the configured **apex velocity**.

3. **Use the pip preview**  
   - While still holding **R**, press and hold **+attack (Mouse1)**.  
   - With `sv_grenade_trajectory_prac_pipreview 1`, the pip now shows the grenade arc **as if you released it at that synthetic apex**.  
   - Rotate your view to test different lineups; the plugin keeps your position/velocity constant so the pip is stable.

4. **Micro-adjust forward with W (optional)**  
   - While latched and holding **R**, press **W** to apply a small forward nudge (`ForwardNudge`) to your apex position.  
   - Release **W** (while still holding R) to remove the nudge and go back to the base apex.  
   - Movement keys do **nothing** when not latched; this only affects the preview state.

5. **Release preview**  
   - Release **R**; once R has been **unpressed for ~3 ticks**, the preview unlatches and teleporting stops.  
   - You remain where you were at the last teleported apex position, and normal movement resumes.

6. **Unintentional throws**  
   - If you accidentally throw a grenade while latched (e.g. you click instead of just pip-previewing), the thrown grenade still exists, but the plugin **gives you an identical grenade back** on the next frame and informs you in chat.

---

## Calibration Tips

To make the pip preview match a real jump-throw landing:

1. **Record a real jump-throw**  
   - Do a real jump-throw with your usual timing on a specific lineup.  
   - Note the landing spot.

2. **Measure real apex state (optional)**  
   - Use `css_jumpnade_dump` at various points in your jump to see real velocities and height.  
   - Approximate the values at the moment you actually release the grenade.

3. **Configure NadeHelper to match**  
   - Start with defaults:
     - `JumpHeightOffset 56`
     - `ForwardOffset 90`
     - `HorizontalVelocity 250`
     - `VerticalVelocity 0`
   - Adjust `ForwardOffset`, `JumpHeightOffset`, and velocities (`css_velh`, `css_velz`, `css_vell`) until:
     - The pip trajectory when holding **R + Mouse1** lands in the same spot as your real jump-throw.

4. **Save your config**  
   - Once you’re happy, copy your tuned values into the JSON config so they persist across restarts.

---

## Troubleshooting

- **Holding R does nothing**  
  - Check that:
    - You have a grenade equipped.
    - You’re on the ground when pressing R.
    - The plugin is loaded: `css_plugins list`.

- **Pip trajectory doesn’t match real jump-throw**  
  - Adjust:
    - Position: `css_jumpoffset`, `css_offset_forward`, `css_offset_right`.
    - Velocity: `css_velh`, `css_velz`, `css_vell`.
  - Compare landing spots between real jump-throws and R+pip preview and iterate.

- **Preview drops out too easily**  
  - Remember there is a **3-tick debounce**: if R is flickering due to key or network issues, you should still stay latched. If you’re still dropping unexpectedly, verify no other plugin is manipulating input/buttons.

- **Unintentional throws not restored**  
  - Ensure you threw the grenade **while latched** (holding R). Throws outside preview are not touched.

- **Config not applying**  
  - Edit the JSON file at:
    ```text
    csgo/addons/counterstrikesharp/configs/plugins/NadeHelper/NadeHelper.json
    ```
  - Then restart the server so CounterStrikeSharp reloads the plugin config.
```
