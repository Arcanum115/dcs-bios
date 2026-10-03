# Changelog

All notable changes to the **C-130J-30** module in this fork are recorded here.
This fork adds Anubis Productions C-130J-30 controls and outputs on top of
DCS-Skunkworks/dcs-bios; entries below are Arcanum115's additions, contained in
`lib/modules/aircraft_modules/C-130J.lua`. Format loosely follows
[Keep a Changelog](https://keepachangelog.com/); dates are ISO (YYYY-MM-DD).

All command IDs and arg numbers come from the C-130J cockpit module's own
`command_defs.lua` / `clickabledata.lua` / `device_init.lua`, so these changes
only expose existing cockpit controls — they add no behaviour to DCS itself.
DCS-BIOS regenerates `doc/json/*.json` on module load, so no manual JSON regen
is needed. Copy the modified `C-130J.lua` into
`%USERPROFILE%\Saved Games\DCS\Scripts\DCS-BIOS\lib\modules\aircraft_modules\`.

## [2026-10-03] — C-130J CARP CNI exports & misc controls

String outputs that read the pilot CNI-MU display (indication id 8) so the
DCSAutoMate CARP automation can pull navigation data back out of the avionics
at build time, plus a few controls added along the way.

### Added — CNI/CARP string outputs
- **`CARP_LEGS_CRS`** — per-waypoint inbound **run-in course** parsed from the
  ACT LEGS page as a compact `LL0N=deg;` map (used to align the CARP run-in with
  the ingress leg).
- **`CARP_LEGS_ELEV`** — per-waypoint **elevation** (ft ASL) from the ACT LEGS
  `----/NNNNNA` field, as `LL0N=ft;` (used to auto-fill PI/DZ elevation).
- **`CARP_PROBE_A` / `CARP_PROBE_B`** — raw ordinal dump of pilot CNI indication
  id 8 (`idx=value;` pairs, elems 1-24 / 25-55) for discovering CNI field
  positions. `parse_indication`-based, `pcall`-guarded (return `""` if
  unavailable, never breaking export).

### Added — controls
- **`PLT_BARO_SET`** (ref-mode baro-set rotary) and **`PLT_BARO_STD`** (push =
  29.92/STD), with **`QFE_PRESSURE`**, **`QFE_TEMPERATURE`**, **`QFE_FIELD_ELEV`**
  string outputs — groundwork for a QFE auto-baro feature.
- **`EXT_FORM_BRT`** — covert/formation-light brightness.
- **`PLT_ICS_RWR_VOLUME`** and **`PLT_ICS_RWR_BUTTON`** — ICS RWR volume knob and
  button.

### Changed
- **`EXT_NAV`**, **`LDG_MOTOR_L`**, **`LDG_MOTOR_R`** — switched to a 3-position
  tumbler (`define3PosTumb`) so the down/`-1` detent is reachable; a centered
  3-pos multiposition switch could only reach mid..up. `EXT_NAV` STEADY = down.

All additions are contained in the existing Arcanum115 delimited block (or a
clearly marked CARP-probe block) at the bottom of `C-130J.lua`. No removals or
signature changes to existing controls.

## [earlier] — C-130J cold-start controls & overhead-LCD outputs

See `PR_dcs-bios.md` (in the DCSAutoMate fork) for the full write-up.

### Added
- Engine start switches (relative-click MOTOR/STOP/RUN/START), engine STOP
  commands, FADEC switches + guards, APU, fire panel, propellers/ATCS/sync,
  bleed air, ice protection, hydraulics, landing gear + parking brake, exterior
  lighting, air conditioning, CNBP, full pilot + copilot CNI-MU (LSK L1-L6 /
  R1-R6, keys, keyboard, EXEC, LEGS, etc.), HUD + REF mode panel, and pilot +
  copilot master caution / master warning.
- Overhead-LCD string outputs: `APU_NG`, `APU_EGT`, `BLEED_AIR_PRESSURE`, cabin /
  cargo air temp, aux hydraulic pressure.
- `safe_numeric_lcd()` helper — substitutes a leading-`0` padded string when an
  overhead LCD reads `"---"` during sensor init, so clients that `int()` the
  value don't crash on partial/non-numeric reads.

## Author

Arcanum115
