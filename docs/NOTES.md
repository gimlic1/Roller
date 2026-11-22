# FFXI Addon — Roller

> [!NOTE]
> **Quick Reference**
> - **Path:** `C:\Windower\addons\Roller\`
> - **Author / Version:** Selindrile (thanks to Balloon, Lorand) / v1.8
> - **Commands:** `//roller`, `//roll`
> - **Load Status:** On-Demand (also aliased as `roll` in Windower init)
> - **Origin:** Third-Party (`https://github.com/Selindrile/Roller.git`)

Automates Corsair Phantom Roll maintenance, Double-Up / Snake Eye / Crooked Cards / Fold / Random Deal logic, and renders a HUD status bar.

## What the Code Does
- Runs `doRoll:loop(3)` every 3 seconds when `autoroll == true` (and `main_job == 'COR'` or `sub_job == 'COR'`). Pauses automatically if Sneak, Invisible, Amnesia, Impairment, or `midRoll` is active, or if `settings.engaged` is true and player status is not `Engaged`.
- Uses `Random Deal` when Double-Up/Snake Eye/Phantom Roll recasts align, uses `Fold` when `Bust` is active (on COR main), casts `Crooked Cards` before `Roll_ind_1` if level > 94 and off cooldown, and casts `Roll_ind_1` then `Roll_ind_2` when missing.
- Hooks the `action` event (`act.category == 6`, ignoring Double-Up `177`) to evaluate the rolled number (`act.targets[1].actions[1].param`) against each roll's Lucky (`[15]`) and Unlucky (`[16]`) numbers in `rollInfo`:
  - Stops on Lucky or 11; otherwise triggers `Snake Eye` + `Double-Up` on `10`, `Lucky - 1`, or Unlucky (`> 6`), or bare `Double-Up` on `< 9` (when Fold is ready and not Crooked) or `< 6` (or when `lastRoll == 11`).
- Tracks Crooked Cards wear (`lose buff` `601`) and Double-Up Chance (`308`), disables `autoroll` on `zone change` / `job change`, and hides/shows the `texts` HUD during zone packets (`0x00D` out / `0x00A` in).

## Commands & Usage
- `//roll` or `//roll rolls` — Print current autoroll state and configured Roll 1 / Roll 2.
- `//roll start|on|go|...` / `//roll stop|off|pause|...` / `//roll roll` — Start, stop, or toggle autorolling.
- `//roll roll roll1` / `//roll roll roll2` — Manually execute Roll 1 or Roll 2 once.
- `//roll roll1 <name>` / `//roll roll2 <name>` — Set Roll 1 or Roll 2 using fuzzy prefix matching (e.g., `sam`, `chaos`, `wiz`, `exp`, `bolt`).
- Presets: `//roll melee`, `//roll exp|cap|cp`, `//roll tp|stp`, `//roll speed|bolt`, `//roll acc`, `//roll ws`, `//roll nuke|matk`, `//roll pet`, `//roll petnuke`.
- `//roll display [on|off|show|hide]`, `//roll engaged [on|off]`, `//roll midroll off`.

## Events, Packets & Technical Hooks
- **Windower Events:** `addon command`, `load`, `action`, `lose buff`, `zone change`, `job change`, `outgoing chunk`, `incoming chunk`
- **Packets:** Outgoing `0x00D` (zone-out hide HUD), Incoming `0x00A` (zone-in show HUD)
- **Dependencies / Libraries:** `luau`, `chat`, `chat.chars`, `packets`, `texts`

## Local Settings & Customizations
- `data/settings.xml` sets defaults: `Roll_ind_1 = 17` (`Corsair's Roll`), `Roll_ind_2 = 19` (`Dancer's Roll`), `engaged = false`, `showdisplay = true`.

## Related Notes
- `FFXI Windower Overview`
- `FFXI Windower Addons Catalog`
