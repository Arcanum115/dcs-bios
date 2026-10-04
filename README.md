[![Release][release-shield]][release-url]
![Downloads][downloads-shield]
[![Issues][issues-shield]][issues-url]
[![License][license-shield]][license-url]

<br />
<div align="center">

<h3 align="center">DCS-BIOS — C-130J-30 fork</h3>

  <p align="center">
    a DCS data exporting tool, with added Anubis Productions C-130J-30 coverage
    <br />
    <br />
    <a href="#installation">Install</a>
    ·
    <a href="#c-130j-carp-testing-beta">CARP (beta)</a>
    ·
    <a href="#modules">Modules</a>
    ·
    <a href="https://github.com/Arcanum115/dcs-bios/releases/latest">Latest release</a>
  </p>
</div>

> [!IMPORTANT]
> **Fork notice — [Arcanum115](https://github.com/Arcanum115)**
>
> This fork adds support for the **Anubis Productions C-130J-30** module
> (cockpit controls, FADEC guards, master caution / master warning, full
> CNI-MU keypad, defensive-systems pages, overhead LCD outputs), plus the
> experimental **CARP airdrop exports** described [below](#c-130j-carp-testing-beta).
>
> The C-130J additions are **working but still WIP**.
>
> **AI-assisted disclaimer:** the C-130J Lua module and parts of this README were
> developed with assistance from Anthropic's Claude AI. All code was hand-verified,
> tested in DCS, and the author ([Arcanum115](https://github.com/Arcanum115)) is
> responsible for what's shipped here. Flagging this so contributors and reviewers
> are informed.

> [!NOTE]
> **Upstream credit**
>
> DCS-BIOS is created and maintained by the DCS-Skunkworks team and its
> contributors. Everything in this repository other than the C-130J-30 additions
> is their work, under their `GPL 3.0` license. For the original project, its
> documentation, its community and its issue tracker, go to the upstream
> repository: **<https://github.com/DCS-Skunkworks/dcs-bios>**
>
> This is an unofficial fork. Please do not raise fork-specific issues upstream.

<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#about-the-project">About The Project</a></li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
        <li><a href="#updating-from-an-older-version">Updating from an older version</a></li>
      </ul>
    </li>
    <li><a href="#c-130j-carp-testing-beta">C-130J CARP Testing (BETA)</a></li>
    <li>
      <a href="#usage">Usage</a>
      <ul>
        <li><a href="#panel-builders">Panel Builders</a></li>
        <li><a href="#software-developers">Software Developers</a></li>
      </ul>
    </li>
    <li>
      <a href="#modules">Modules</a>
      <ul>
        <li><a href="#official-dcs-modules">Official DCS Modules</a></li>
        <li><a href="#full-fidelity-mods">Full-Fidelity Mods</a></li>
        <li><a href="#flaming-cliffs-mods">Flaming Cliffs Mods</a></li>
        <li><a href="#adding-a-mod">Adding a Mod</a></li>
      </ul>
    </li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
  </ol>
</details>

## About The Project

DCS-BIOS is an `Export.lua` script for use with [DCS: World][dcs-url], enabling
external hardware and software to interact with the clickable cockpit of a DCS
aircraft. It streams cockpit state out of the sim and accepts commands back in,
so physical panels, button boxes and automation tools can read gauges and throw
switches exactly as a pilot would.

This fork tracks upstream and adds coverage for the Anubis Productions
**C-130J-30**, including the CARP airdrop work that is still in beta.

## Getting Started

### Prerequisites

- **DCS World 2.9.x**
- For the C-130J coverage: the Anubis Productions **C-130J-30** mod installed

#### Find your DCS Scripts folder

Start by finding your DCS Saved Games folder. On Windows, this is likely either:

- `C:\Users\USERNAME\Saved Games\DCS`
- `C:\Users\USERNAME\Saved Games\DCS.openbeta`

Within that folder you'll find a folder called `Scripts`. Create one if it does
not exist. The final path should look like:

- `C:\Users\USERNAME\Saved Games\DCS\Scripts`
- `C:\Users\USERNAME\Saved Games\DCS.openbeta\Scripts`

### Installation

> [!IMPORTANT]
> **Already running an older DCS-BIOS?** Do not copy the new files over the old
> ones — see [Updating from an older version](#updating-from-an-older-version)
> first, then come back to step 3.

1. Go to the [latest release][latest-release-url]
2. Download `DCS-BIOS.zip`
3. Extract the zip. It contains a single `DCS-BIOS` folder.
4. Copy that `DCS-BIOS` folder into the `Scripts` folder you found above, so you
   end up with `...\Saved Games\DCS\Scripts\DCS-BIOS\BIOS.lua`
5. Open (or create) `...\Saved Games\DCS\Scripts\Export.lua`. If the file does not
   exist, create it. If it already exists, **add to the end of it** — do not
   replace it, or you'll remove the hooks for your other mods and tools:

   ```lua
   dofile(lfs.writedir() .. [[Scripts\DCS-BIOS\BIOS.lua]])
   ```

6. Start DCS and load a mission in a supported aircraft.

> [!NOTE]
> That single line is all DCS-BIOS needs. (The CARP automation adds a second,
> separate line — see [CARP Testing](#c-130j-carp-testing-beta).)

> [!NOTE]
> The release zip deliberately does **not** contain an `Export.lua`, so
> installing or updating can never overwrite the one you already have and wipe
> hooks for your other mods and tools.

### Updating from an older version

Replacing the folder wholesale is the supported way to update. Merging the new
files into an old install leaves stale generated files behind in `DCS-BIOS\doc\json`,
and any client that reads those will use **out-of-date output addresses** — the
usual symptom is a value that looks correct in the cockpit but never registers in
your panel or automation tool.

1. **Quit DCS** completely.
2. **Delete** your existing folder outright:

   ```
   %USERPROFILE%\Saved Games\DCS\Scripts\DCS-BIOS
   ```

   (Use `...\Saved Games\DCS.openbeta\Scripts\DCS-BIOS` on Open Beta.)

3. **Install the new version** by following steps 1–4 of
   [Installation](#installation) above.
4. **Leave `Export.lua` alone.** It lives outside the `DCS-BIOS` folder and is not
   part of the zip, so your existing hooks survive. Just confirm the DCS-BIOS
   `dofile` line is still present.
5. **Restart DCS first, then restart your DCS-BIOS client.** DCS regenerates
   `DCS-BIOS\doc\json` when it loads a module, and clients typically read those
   address definitions once at startup. A client started before the regeneration
   finishes will be reading the old addresses.

> [!TIP]
> Output addresses shift whenever a module's control set changes, which includes
> most updates to this fork. If something that used to work goes quiet after an
> update, the DCS-then-client restart order in step 5 is the first thing to check.

## C-130J CARP Testing (BETA)

> [!WARNING]
> **This is experimental and under active development.** The outputs below are
> beta, their parsing is best-effort, and field positions can move when the
> cockpit mod is updated. Do not depend on them for anything you care about yet.
> Everything here is guarded so that a parsing failure returns an empty string
> rather than breaking the export stream.

### What CARP is

**CARP** stands for **Computed Air Release Point** — the point in space where
cargo has to leave the aircraft so that it lands on the drop zone. It is not the
same as the target: a bundle released over the drop zone will overshoot it,
because it keeps the aircraft's forward momentum and then drifts under its
parachute. Working out the release point means accounting for ground speed,
drop altitude, the wind through the drop, and how that specific parachute and
load behave.

In the real C-130J the crew programs this on the **CNI-MU**, across the
`CARP INIT` pages — entering the point of impact, the run-in course, drop zone
dimensions, the load type and parachute, drop speed, and the winds. The avionics
then compute the release point and drive the green light for the loadmaster.

### What this fork adds

DCS-BIOS can already *operate* the CNI-MU keypad, but an external tool had no way
to **read back** what the avionics were showing. These string outputs parse the
pilot CNI-MU display so a client can pull navigation data out of the aircraft and
build a CARP solution from it:

| Output | What it gives you |
|-|-|
| `CARP_LEGS_CRS` | Per-waypoint inbound **run-in course** from the `ACT LEGS` page, as a compact `LL01=008;LL02=012;` map. Lets a client align the CARP run-in with the actual ingress leg instead of asking you to type a heading. |
| `CARP_LEGS_ELEV` | Per-waypoint **elevation** in feet ASL from the same page, as `LL01=1250;`. Used to auto-fill the point-of-impact / drop-zone elevation. |
| `CARP_PROBE_A` | Diagnostic raw dump of pilot CNI indication elements 1–24, as `idx=value;` pairs (capped at 150 characters). |
| `CARP_PROBE_B` | The same for elements 25–55. |

The two `CARP_PROBE_*` outputs exist for **discovery**, not for normal use — they
let you see which ordinal position a given CNI field currently occupies, which is
how the parsers above were built. Expect to need them again if a cockpit mod
update shuffles the display.

`CARP_LEGS_CRS` and `CARP_LEGS_ELEV` only return data **while the `ACT LEGS` page
is actually displayed** on the pilot CNI-MU. An empty string means the page isn't
up, not that the export is broken.

Also added alongside this work, as groundwork for automatic altimeter setting:
the pilot baro set rotary (`PLT_BARO_SET`), the STD push (`PLT_BARO_STD`), and
`QFE_PRESSURE` / `QFE_TEMPERATURE` / `QFE_FIELD_ELEV`.

### Required `Export.lua` setup for CARP

DCS-BIOS itself only needs its own line. **CARP needs a second one**, because the
automation side runs through the DCSAutoMate export hook. For CARP to work
properly, `...\Saved Games\DCS\Scripts\Export.lua` must contain both:

```lua
dofile(lfs.writedir() .. [[Scripts\DCS-BIOS\BIOS.lua]])
dofile(lfs.writedir()..[[Scripts\DCSAutoMateExport.lua]])
```

Load DCS-BIOS first. The second line loads `Scripts\DCSAutoMateExport.lua`, which
ships with the companion [DCSAutoMate fork](https://github.com/Arcanum115/DCSAutoMate) —
copy that file into `...\Saved Games\DCS\Scripts\` next to your `Export.lua`.
Leave any other hooks in the file alone; just append what's missing.

> [!TIP]
> If CARP sits there doing nothing, a missing second line is the first thing to
> check.

### Trying it out

1. Install this release and load the C-130J-30.
2. Point any DCS-BIOS client at the aircraft — the simplest check is to watch the
   `CARP_LEGS_CRS` string while you bring up `ACT LEGS` on the pilot CNI-MU. It
   should populate with one entry per waypoint and go empty when you leave the page.
3. The companion [DCSAutoMate fork](https://github.com/Arcanum115/DCSAutoMate)
   drives the full `CARP INIT` page sequence from these exports and draws a
   run-in / drop-zone plan view while it does.

If something parses wrongly, the useful thing to report is the `CARP_PROBE_A` and
`CARP_PROBE_B` strings captured at the moment the page looked wrong, plus which
CNI page was displayed. Open an issue on [this fork][issues-url].

## Usage

### Panel Builders

You don't need to be a programmer or electrical engineer to build your own
panels. The [DCS-BIOS User Guide][user-guide-url] included in this repository has
step-by-step instructions for connecting a panel to DCS using the beginner-friendly
[Arduino microcontroller platform](http://arduino.cc), without writing code
yourself.

Download `Arduino_Tools.zip` from the [latest release][latest-release-url] for the
Arduino library, the generated `Addresses.h`, and the serial helper tools.

#### Connect the DCS-BIOS stream to your serial ports

`socat` (bundled in `Arduino_Tools.zip`) or any DCS-BIOS bridge client can connect
the export stream to your device.

> [!IMPORTANT]
> If using `socat`, the files in the .zip must be unzipped directly into the socat
> folder. The path **must** be `/socat/socat.exe`

#### Debugging

If you work a lot with hardware, it helps to log and replay DCS-BIOS data. Two
scripts in [Programs/tools](Programs/tools/) do this:

- `python connect-logger.py` logs all DCS-BIOS data to `dcsbios_data.json`. Start
  the logger **before** loading a mission so it captures the mission-start message.
- `python replay-log.py` asks for a serial port and replays the data to it, looping
  forever until you close it. The first message is not repeated, since that is
  usually the mission-start message and should only be sent once.
- `dcsbios_data.json` holds the logged data in hex. If you know the DCS-BIOS
  message format you can hand-edit it. The included sample is an A-10C recording
  with a blinking Master Caution light.

### Software Developers

The [Developer Guide][developer-guide-url] in this repository explains how to
connect to and interpret the DCS-BIOS export stream, and how to send commands to
operate cockpit controls. Client libraries exist for several languages; the
developer guide covers the wire format if you'd rather write your own.

## Modules

> [!NOTE]
> Aircraft with multiple variants (e.g. A-10C / A-10C II, F-14A / F-14B) are
> treated as a single module. This list reflects the modules with DCS-BIOS
> definitions in **this** repository.

### Official DCS Modules

| Module | Status |
|-|-|
| A-10C / A-10C II | ✅ |
| AH-64D | ✅ |
| AJS-37 | ✅ |
| AV-8B N/A | ✅ |
| Bf 109 K-4 | ✅ |
| C-101CC / C-101EB | ✅ |
| CH-47F | ✅ |
| Christen Eagle II | ✅ |
| F-4E | ✅ |
| F-5E-3 | ✅ |
| F-14A / F-14B | ✅ |
| F-15E | ✅ |
| F-16C | ✅ |
| F-86F Sabre | ✅ |
| F4U-1D | ✅ |
| F/A-18C | ✅ |
| Fw 190 A-8 | ✅ |
| Fw 190 D-9 | ✅ |
| I-16 | ✅ |
| JF-17 | ✅ |
| Ka-50 / Ka-50 III | ✅ |
| L-39C / L-39ZA | ✅ |
| M-2000C / M-2000D | ✅ |
| MB-339A / MB-339APAN | ✅ |
| Mi-8MTV2 | ✅ |
| Mi-24P | ✅ |
| MiG-15bis | ✅ |
| MiG-19P | ✅ |
| MiG-21Bis | ✅ |
| MiG-29A Fulcrum | ✅ |
| Mirage F1 (BE / CE / EE) | ✅ |
| Mosquito FB Mk.VI | ✅ |
| OH-58D | ✅ |
| P-47D | ✅ |
| P-51D / TF-51D | ✅ |
| SA342 (L / M / Minigun / Mistral) | ✅ |
| Spitfire LF Mk.IX | ✅ |
| UH-1H | ✅ |
| Yak-52 | ✅ |
| Flaming Cliffs (all modules) | ✅ |
| NS430 (standalone + C-101 / L-39 / Mi-8 / SA342 variants) | ✅ |
| Supercarrier | ✅ |

### Full-Fidelity Mods

Mods with their own dedicated DCS-BIOS control definitions:

| Module | Status | Link |
|-|-|-|
| A-4E-C | ✅ | [GitHub](https://github.com/heclak/community-a4e-c) |
| A-29B | ✅ | [GitHub](https://github.com/luizrenault/a-29b-community) |
| AH-6J | ✅ | [DCS Forums](https://forum.dcs.world/topic/228394-helicopter-efm-demo) |
| Alphajet | ✅ | |
| **C-130J-30** † | 🚧 WIP | [Developer](https://github.com/Arcanum115) |
| Edge 540 / Extra 330SR | ✅ | [Developer](http://virtualairrace.com/downloads/) |
| F-22A | ✅ | |
| MH-60R | ✅ | |
| T-45 Goshawk | ✅ | [DCS Forums](https://forum.dcs.world/topic/203816-vnao-t-45-goshawk/) |

> [!NOTE]
> † **C-130J-30:** the focus of this fork. Working but still WIP; the CARP exports
> are [beta](#c-130j-carp-testing-beta). The Lua module was developed with
> assistance from Anthropic's Claude AI — see the fork notice at the top.

Additionally recognised for export (common data works, no dedicated control set):
Bell 47G, UH-60L / Black Hawk, EA-18G, F/A-18E / F/A-18F, F-16D and F-16I variants,
VNAO Ready Room.

### Flaming Cliffs Mods

- AC-130
- Civil Aircraft mod
- MIG-23UB Project
- Mirage F.1
- PAK-FA Project
- SU-30 FAMILY PROJECT
- Upuaut's Bell-47G
- Virtual Cockpits
- VSN-Mods

### Adding a Mod

DCS-BIOS supports many community mods out-of-the-box.

To add a Flaming-Cliffs-based mod that isn't supported yet, add the following to
the bottom of `DCS-BIOS/lib/AircraftList.lua`:

```lua
add("PlaneName", false)
```

> [!TIP]
> To get the correct plane name, open the DCS-BIOS Reference Tool (`MetadataStart`)
> while flying that plane and see what value `_ACFT_NAME` has.

## Contributing

Fork-specific contributions — C-130J-30 controls, CARP work, documentation — are
welcome here. Please [open an issue][issues-url] or a pull request, and see
[CONTRIBUTING.md](CONTRIBUTING.md) first.

> [!IMPORTANT]
> Improvements that aren't specific to the C-130J-30 belong upstream, where they
> benefit everyone — see the upstream credit note at the top of this README.

## License

Distributed under the `GPL 3.0` license. See [LICENSE](LICENSE) for the full text
and the copyright notices of the original authors.

The copy of `socat` that ships with DCS-BIOS is licensed under `GPL 2.0` (see
[Programs/socat/COPYING](Programs/socat/COPYING)).

## Acknowledgments

- [Best-README-Template](https://github.com/othneildrew/Best-README-Template)
- [luacheck](https://github.com/lunarmodules/luacheck)
- [LuaUnit](https://github.com/bluebird75/luaunit)
- [StyLua](https://github.com/JohnnyMorganz/StyLua)
- [lua-language-server](https://github.com/LuaLS/lua-language-server)

[release-shield]: https://img.shields.io/github/v/release/Arcanum115/dcs-bios?style=for-the-badge
[release-url]: https://github.com/Arcanum115/dcs-bios/releases/latest
[downloads-shield]: https://img.shields.io/github/downloads/Arcanum115/dcs-bios/total?style=for-the-badge
[issues-shield]: https://img.shields.io/github/issues/Arcanum115/dcs-bios.svg?style=for-the-badge
[issues-url]: https://github.com/Arcanum115/dcs-bios/issues
[license-shield]: https://img.shields.io/github/license/Arcanum115/dcs-bios.svg?style=for-the-badge
[license-url]: LICENSE

[dcs-url]: http://www.digitalcombatsimulator.com
[latest-release-url]: https://github.com/Arcanum115/dcs-bios/releases/latest
[user-guide-url]: Scripts/DCS-BIOS/doc/userguide.adoc
[developer-guide-url]: Scripts/DCS-BIOS/doc/developerguide.adoc
