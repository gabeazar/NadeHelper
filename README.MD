# NadeHelper (CounterStrikeSharp Plugin)

NadeHelper is a CounterStrikeSharp plugin for **CS2** that lets you:

- Preview **jump-throw** and **W-jump** grenade lineups in practice servers using the grenade pip.
- Simulate the player at a physically correct **jump apex** (position + velocity) using real `sv_gravity` and `sv_jump_impulse`.
- Tune apex offsets and velocities live via commands to match the exact “grunt” jump-throw release moment.
- Keep all server movement and gravity cvars untouched (no `sv_gravity` hacks).

Author: `gabeazar`  
Plugin name: `NadeHelper`  
Version: `0.10`

---

## Features

- **Hold-Reload Physics-Based Apex Simulation**  
  While on the ground with a grenade equipped, holding **Reload (R)** latches your state and teleports you to a **physics-derived jump apex** every tick:
  - Apex height is computed from **real** `sv_gravity` and `sv_jump_impulse`.
  - Horizontal displacement comes from your **actual movement speed** at latch time.
  - Position offsets: configurable forward/right/up offsets on top of the computed apex.
  - Velocity offsets: configurable forward/side/vertical velocity tweaks on top of the computed apex velocity.
  - Optional “air simulation”: forces off-ground flag for a mid-air look.  
  The grenade pip (e.g. `sv_grenade_trajectory_prac_pipreview 1`) then shows the trajectory as if you released the grenade at that jump-throw apex.

- **No Gravity / Movement Cvar Changes**  
  NadeHelper **never modifies** `sv_gravity`, `sv_jump_impulse`, or any movement cvars. It only reads them to compute a physically correct jump, and then uses teleports to simulate the release state.

- **Debounced R Release**  
  To avoid flicker or packet-loss issues, R must be released for **3 consecutive ticks** before the preview unlatches. Brief input gaps won’t drop you out of preview and cause jitter.

- **W-Based Micro Nudge Only While Latched**  
  While latched and holding **R**, pressing **W** applies a small configurable forward nudge to your apex position and an optional extra forward velocity. Releasing W (while still holding R) removes the nudge. Movement keys have **no effect** when not latched.

- **Apex Position & Velocity Tuning**  
  Live-adjust:
  - Extra apex height (`JumpHeightOffset`)
  - Extra forward/right offset (`ForwardOffset`, `RightOffset`)
  - Extra apex velocity tweaks (`VerticalVelocity`, `HorizontalVelocity`, `LateralVelocity`)
  - Forward nudge distance and velocity (`ForwardNudge`, `ForwardNudgeVelocity`)  
  so that the pip preview matches where a real jump-throw (the “grunt” release) would land on your server.

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
  "Version": 2,
  "JumpHeightOffset": 0.0,
  "ForwardOffset": 0.0,
  "RightOffset": 0.0,
  "VerticalVelocity": 0.0,
  "HorizontalVelocity": 0.0,
  "LateralVelocity": 0.0,
  "ForwardNudge": 3.0,
  "ForwardNudgeVelocity": 29.99,
  "AirSimulation": true
}
```

**Options:**

- `JumpHeightOffset` (float, default: `0.0`)  
  Extra height (Z, units) **added on top of the physics-computed jump apex**.  
  By default, the plugin uses the real apex from `sv_gravity` and `sv_jump_impulse`. Adjust this slightly if your perfect jump-throw release is a bit before/after the true apex.

- `ForwardOffset` (float, default: `0.0`)  
  Extra forward offset (along your view direction) **added on top of the predicted horizontal apex position**.  
  The base prediction already uses your current ground velocity; use this for small corrections.

- `RightOffset` (float, default: `0.0`)  
  Extra side offset (to the right of your view) from the predicted apex. Useful for lineups involving small strafes that the simple prediction doesn’t fully capture.

- `VerticalVelocity` (float, default: `0.0`)  
  Extra vertical velocity at the release moment (units/sec).  
  The physics apex itself is `0` vertical velocity; positive values simulate slightly **before** the apex, negative values simulate slightly **after**.

- `HorizontalVelocity` (float, default: `0.0`)  
  Extra forward (view-direction) velocity at release (units/sec).  
  The plugin already uses your real ground horizontal speed; use this to fine-tune jump-throws that feel slightly faster/slower than the simple model.

- `LateralVelocity` (float, default: `0.0`)  
  Extra sideways velocity at release (units/sec).  
  Use this for jump-throws where you are still moving slightly left/right at the release moment.

- `ForwardNudge` (float, default: `3.0`)  
  Extra forward distance (units) applied when you hold **R + W** while latched. Acts as a micro-adjustment tool in preview to simulate very small changes in jump distance.

- `ForwardNudgeVelocity` (float, default: `29.99`)  
  Additional forward velocity (units/sec) applied when you hold **R + W** while latched.  
  This is a small velocity “boost” on top of your computed horizontal speed, useful to approximate W-jumps.

- `AirSimulation` (bool, default: `true`)  
  When `true`, the plugin clears the ground flag each tick while latched so you appear mid-air; position/velocity are still fully controlled via teleport and the values above.  
  When `false`, the engine may try to snap you to the ground, which can look odd depending on map geometry.

---

## Usage

### Commands

Use these commands in the **server console** or in your own console (with appropriate permissions) to tune NadeHelper while in-game:

- `css_jumpoffset <value>`  
  Set extra apex height added to the computed jump apex (e.g. `css_jumpoffset 4`).

- `css_offset_forward <value>`  
  Set extra forward apex offset (units along your current view, e.g. `css_offset_forward 4`).

- `css_offset_right <value>`  
  Set extra right apex offset (units to your view’s right, e.g. `css_offset_right 1`).

- `css_velz <value>`  
  Set extra vertical velocity at release (e.g. `css_velz 0` for true apex, slightly >0 or <0 to bias earlier/later).

- `css_velh <value>`  
  Set extra forward (view-direction) velocity at release (e.g. `css_velh 10` for a small extra push).

- `css_vell <value>`  
  Set extra lateral (sideways) velocity at release.

- `css_wnudge <value>`  
  Set forward nudge distance applied while **R + W** are held during preview (e.g. `css_wnudge 3`).

- `css_wnudge_vel <value>`  
  Set additional forward velocity applied while **R + W** are held during preview (e.g. `css_wnudge_vel 30`).

- `css_jumpairtoggle`  
  Toggle “air simulation” on/off (controls whether the plugin forces you off-ground visually while latched).

- `css_jumpnade_dump`  
  Print your current position and velocity to chat (useful for understanding your real movement/jump behavior for calibration).

---

### In-Game Flow (Jump-Throw Pip Preview)

1. **Equip a grenade**  
   Use any grenade: HE, Flash, Smoke, Molotov, Incendiary, Decoy.

2. **Latch into preview**  
   - Stand on the ground at your lineup spot.  
   - Aim where you want the grenade to go.  
   - Hold **Reload (R)** with the grenade out.  
   - The plugin:
     - Reads `sv_gravity` and `sv_jump_impulse` (without changing them).
     - Computes the **time to jump apex** and the **apex height** based on real physics.
     - Uses your current horizontal velocity to predict where you would be at that apex.
     - Applies any configured offsets (height/forward/right + velocity tweaks).
     - Teleports you into that apex state and starts re-teleporting you every tick while R is held.

3. **Use the pip preview**  
   - While still holding **R**, press and hold **+attack (Mouse1)**.  
   - With `sv_grenade_trajectory_prac_pipreview 1`, the pip now shows the grenade arc **as if you released it at that physics-based jump apex**.  
   - Rotate your view to test different lineups; the plugin keeps your position/velocity constant so the pip is stable and repeatable.

4. **Micro-adjust forward with W (optional)**  
   - While latched and holding **R**, press **W** to apply:
     - A small forward position nudge (`ForwardNudge`).
     - A small additional forward velocity (`ForwardNudgeVelocity`).  
   - Release **W** (while still holding R) to remove the nudge and go back to the base apex state.  
   - This helps you simulate subtle W-jump differences without actually jumping.

5. **Release preview**  
   - Release **R**; once R has been **unpressed for ~3 ticks**, the preview unlatches and teleporting stops (debounced to avoid jitter).  
   - You then fall and move normally again under the server’s real gravity and movement settings.

---

## Calibration Tips

Because NadeHelper is already using your server’s real `sv_gravity` and `sv_jump_impulse`, you mostly need to **fine-tune** offsets to align with the exact moment you normally release (the “grunt” jump-throw):

1. **Record a real jump-throw**  
   - On a specific lineup, do a real jump-throw with your usual timing.  
   - Note the landing spot.

2. **Use the default physics-based preview**  
   - Ensure your config offsets are near default:
     - `JumpHeightOffset 0`
     - `ForwardOffset 0`
     - `RightOffset 0`
     - `VerticalVelocity 0`
     - `HorizontalVelocity 0`
     - `LateralVelocity 0`  
   - Hold **R + Mouse1** and compare the pip landing to your real jump-throw.

3. **Apply small corrections**  
   - If the pip lands **short/long**, adjust:
     - Position: `css_offset_forward`, `css_jumpoffset`.
     - Velocity: small tweaks via `css_velh`, `css_velz`.  
   - If it lands **left/right off**, adjust:
     - `css_offset_right` and possibly `css_vell`.  
   - Iterate until the pip preview’s landing matches your real throw.

4. **Simulate W-jumps**  
   - For W-jump specific lineups, tune:
     - `ForwardNudge` and `ForwardNudgeVelocity` (via `css_wnudge` / `css_wnudge_vel`).  
   - These only apply while you hold **R + W**, so you can quickly compare normal jump-throws vs W-jumps.

5. **Save your config**  
   - Once you’re happy, copy your tuned values into the JSON config so they persist across restarts.

---

## Troubleshooting

- **Holding R does nothing**  
  - Check that:
    - You have a grenade equipped.
    - You’re on the ground when pressing R.
    - The plugin is loaded: `css_plugins list`.

- **Pip trajectory doesn’t match real jump-throw**  
  - Remember the plugin uses **real** `sv_gravity` and `sv_jump_impulse` and your current movement speed.  
  - Use:
    - Position tweaks: `css_jumpoffset`, `css_offset_forward`, `css_offset_right`.  
    - Velocity tweaks: `css_velh`, `css_velz`, `css_vell`.  
  - Compare landing spots between real jump-throws and R+pip preview and iterate.

- **Preview drops out too easily**  
  - There is a **3-tick debounce** on R release.  
  - If you still drop unexpectedly, verify:
    - No other plugin is manipulating input/buttons.
    - Your keybind is correctly bound to `+reload`.

- **Config not applying**  
  - Edit the JSON file at:
    ```text
    csgo/addons/counterstrikesharp/configs/plugins/NadeHelper/NadeHelper.json
    ```
  - Then restart the server so CounterStrikeSharp reloads the plugin config.
---


