# DCS-BIOS v0.11.4 (Arcanum115 fork)

> [!NOTE]
> **Fork notice — [Arcanum115](https://github.com/Arcanum115)**
>
> This fork adds Anubis Productions **C-130J-30** coverage on top of
> [DCS-Skunkworks/dcs-bios](https://github.com/DCS-Skunkworks/dcs-bios).
> This release merges current upstream and fixes two C-130J switches that
> did not actually move in-game.

## What's updated

**Merged current upstream DCS-Skunkworks/dcs-bios.** Brings in the official
C-130J module, the MiG-29A, a large batch of F-14 additions (CDNU, ECMD, PTID,
ALE-47, TIS, DFCS, AAI, B (U) standalone controls, NVG controls), AJS-37 IFF
transponder, CH-47F CDU data updates, plus core fixes — system-time export,
centralized drum displays, and faster rounding.

**Fixed — `ATCS` and `PROP_SYNC` never engaged.** Upstream defined these with an
inverted manual range, so commanding state `1` drove the cockpit argument to `0`
and the switches stayed off. Both are back to plain toggle switches and now work.

**Added — C-130J CARP / CNI string outputs.** These read the pilot CNI-MU display
so an external client can pull navigation data back out of the avionics:

- `CARP_LEGS_CRS` — per-waypoint inbound run-in course from the ACT LEGS page
- `CARP_LEGS_ELEV` — per-waypoint elevation (ft ASL) from the same page
- `CARP_PROBE_A` / `CARP_PROBE_B` — raw ordinal dump of CNI indication id 8, for
  locating CNI fields; both are `pcall`-guarded and never break the export

**Added — C-130J controls.** Pilot baro set rotary (`PLT_BARO_SET`) and STD push
(`PLT_BARO_STD`) with `QFE_PRESSURE` / `QFE_TEMPERATURE` / `QFE_FIELD_ELEV`
outputs, covert/formation light brightness (`EXT_FORM_BRT`), and the ICS RWR
volume knob and button (`PLT_ICS_RWR_VOLUME` / `PLT_ICS_RWR_BUTTON`).

**Fixed — unreachable switch detents.** `EXT_NAV`, `LDG_MOTOR_L` and
`LDG_MOTOR_R` are now 3-position tumblers, so the down detent is reachable
(previously a centered 3-position switch could only reach mid and up).
`EXT_NAV` STEADY is the down position.

> [!IMPORTANT]
> **Output addresses shifted in this release.** The upstream merge changed the
> control set, which moves DCS-BIOS output addresses. Any client that caches
> addresses must be restarted after DCS — see step 5 below.

## Installing — replace the whole folder

> [!WARNING]
> Replace the `DCS-BIOS` folder outright; do not copy the new files over the old
> ones. A merge leaves stale generated files in `doc\json`, and a client reading
> those will use wrong output addresses.

1. **Quit DCS** completely.

2. **Delete your existing DCS-BIOS folder:**

   ```
   %USERPROFILE%\Saved Games\DCS\Scripts\DCS-BIOS
   ```

   (Use `...\Saved Games\DCS.openbeta\Scripts\DCS-BIOS` on Open Beta.)

3. **Download `DCS-BIOS.zip`** from this release and extract it. It contains a
   single `DCS-BIOS` folder.

4. **Copy that `DCS-BIOS` folder into your Scripts folder**, so you end up with
   `...\Saved Games\DCS\Scripts\DCS-BIOS\BIOS.lua` again.

5. **Restart DCS first, then restart your DCS-BIOS client.** DCS regenerates
   `doc\json` when it loads a module; a client started before that regeneration
   reads the old addresses. The usual symptom is a value that looks correct in
   the cockpit but never registers in the client.

### Your `Export.lua` is intentionally left alone

This zip deliberately **does not include `Export.lua`**, so any other export
hooks you run (SRS, TacView, other mods) survive the upgrade. Just confirm your
`...\Saved Games\DCS\Scripts\Export.lua` still contains the DCS-BIOS line —
adding it only if it is missing:

```lua
dofile(lfs.writedir() .. [[Scripts\DCS-BIOS\BIOS.lua]])
```

## Assets

| File | Contents |
| --- | --- |
| `DCS-BIOS.zip` | The `DCS-BIOS` folder for `Saved Games\DCS\Scripts\` (unit tests excluded) |
| `Arduino_Tools.zip` | The `Programs` folder — Arduino library and serial/socat helper tools |
| `Addresses.h` | Generated address header for Arduino sketches |

## Requirements

- DCS World 2.9.x
- Anubis Productions **C-130J-30** mod, for the C-130J coverage

## Credits

- **Upstream DCS-BIOS** — [DCS-Skunkworks](https://github.com/DCS-Skunkworks/dcs-bios)
- **C-130J cockpit module** — Anubis Productions
- **C-130J DCS-BIOS additions** — [Arcanum115](https://github.com/Arcanum115)

Distributed under `GPL 3.0`, the same license as upstream DCS-BIOS.
