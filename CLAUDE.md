# CLAUDE.md

## Project

**OWB - The Think Tank** (`tnkd`) — a Hearts of Iron IV mod, sub-mod of *Old World Blues*
(declared as a dependency in `mod_folder/descriptor.mod`). Targets HOI4 `1.17.*`.
All content lives under `mod_folder/`.

Primary country tag: **TNK** (the Think Tank). Other tags referenced: NCR, CES, VEG, SLE, etc.

## Layout

| Path | Contents |
|---|---|
| `mod_folder/descriptor.mod` | Mod metadata (version, deps, supported game version) |
| `mod_folder/common/dynamic_modifiers/tnkd_dynamic_modifiers.txt` | Dynamic modifier definitions |
| `mod_folder/common/scripted_effects/tnkd_scripted_effect.txt` | Scripted effects, incl. the `update_*` variable helpers and `newline` |
| `mod_folder/common/decisions/tnkd_decisions.txt` | Decisions & missions |
| `mod_folder/common/national_focus/Think Tank (TNK) Focus.txt` | The TNK focus tree |
| `mod_folder/common/ideas/tnkd_tnk_ideas.txt` | Ideas |
| `mod_folder/common/on_actions/tnkd_on_actions.txt` | Polled triggers (e.g. fire `nf_tnk.62` when `MOJ` ceases to exist) |
| `mod_folder/events/tnkd.txt` | Events (`nf_tnk.*`) |
| `mod_folder/interface/tnkd_ideas.gfx` | `spriteType` definitions for idea / modifier icons |
| `mod_folder/localisation/tnkd_*_l_english.yml` | English localisation |

Files are UTF-8 (no BOM) with LF line endings. The focus tree is tab-indented; some
decision blocks use spaces — **match the surrounding file** rather than reformatting.

## Core pattern: dynamic-modifier-backed variables

Several mechanics (Decohesion, Brain Drain, Broken Mojave, Angels of Death, Ingenuity…)
are modeled as a **dynamic modifier whose magnitudes are script variables**. Tuning the
mechanic means changing a variable; the dynamic modifier reads it live.

### Anatomy

1. **Dynamic modifier** (`tnkd_dynamic_modifiers.txt`) maps game modifiers to variables:
   ```
   tnk_decohesion_dynamic_modifier = {
       icon = GFX_idea_tnkd_fractured_mind
       stability_factor = tnk_decohesion_stability_var
       army_org_factor  = tnk_decohesion_org_var
   }
   ```
   `icon` references a `spriteType` `name` defined in an interface `.gfx` file. To change
   an icon, point it at an existing `GFX_idea_*` sprite, or add a new
   `spriteType { name = "GFX_idea_tnkd_..." texturefile = "gfx/interface/ideas/....dds" }`
   in `tnkd_ideas.gfx`.

2. **Initialization** — set the variables and `add_dynamic_modifier` **once**, usually in
   an event (e.g. Decohesion in `nf_tnk.33`, Broken Mojave in `nf_tnk.62`):
   ```
   set_variable = { tnk_decohesion_stability_var = -0.02 }
   set_variable = { tnk_decohesion_org_var = -0.01 }
   add_dynamic_modifier = { modifier = tnk_decohesion_dynamic_modifier }
   ```

3. **Incremental updates** — never edit the variable inline at the call site. Set a
   `_change` temp var and call the matching `update_*` scripted effect:
   ```
   set_temp_variable = { tnk_decohesion_stability_var_change = -0.02 }
   update_tnk_decohesion_stability_var = yes
   ```

4. **`update_*` scripted effect** (`tnkd_scripted_effect.txt`) — shows a generic tooltip and
   applies the change **unconditionally** (no `has_dynamic_modifier` guard), so the change is
   visible and the variable accumulates even before the modifier is added:
   ```
   update_tnk_decohesion_stability_var = {
       custom_effect_tooltip = tnk_decohesion_stability_var_tt
       add_to_variable = { tnk_decohesion_stability_var = tnk_decohesion_stability_var_change }
   }
   ```

### Why this shape
- Centralizes the math + tooltip in one place; call sites are two lines and self-documenting.
- `add_to_variable` covers first use too: a missing variable reads as `0`, so the first
  `add_to_variable` equals `set_variable`. No `if has_variable / else` branch needed.
- No `has_dynamic_modifier` guard: the change always applies and the tooltip always shows, so
  the player sees the effect even when the modifier isn't active yet, and the variable keeps
  accumulating. The dynamic modifier reads the variable live once it's present.

### Variant: no single init point (Brain Drain)
Brain Drain has no init event — it is established by whichever focus first triggers it.
Its `update_tnk_brain_drain_var` therefore **adds the modifier itself, guarded so it only
adds once**:
```
update_tnk_brain_drain_var = {
    hidden_effect = {
        if = {
            limit = { NOT = { has_dynamic_modifier = { modifier = tnk_brain_drain_dynamic_modifier } } }
            add_dynamic_modifier = { modifier = tnk_brain_drain_dynamic_modifier }
        }
    }
    custom_effect_tooltip = tnk_brain_drain_var_tt
    add_to_variable = { tnk_brain_drain_var = tnk_brain_drain_var_change }
}
```
The `NOT has_dynamic_modifier` guard matters: **`add_dynamic_modifier` stacks** if called
repeatedly, multiplying the effect. The same `update_` call serves both worsening
(`_change = -0.02`) and improving (`_change = 0.5`) focuses — the sign drives the tooltip color.

## Generic, value-interpolating tooltips

Tooltips read the values dynamically instead of hardcoding numbers, and rely on
**automatic loc coloring** (no manual `§R`/`§G`):
```
tnk_decohesion_stability_var_tt:0 "Modifies §HStability§! for §Y$tnk_decohesion_dynamic_modifier$§! by [?tnk_decohesion_stability_var_change|+%] (Current: [?tnk_decohesion_stability_var|+%])"
```
- `$modifier_name$` — substitutes the dynamic modifier's localized name.
- `[?var|format]` — prints the variable. Format codes: `%` = ×100 with `%` sign;
  `+` = force sign and color **positive = green / negative = red** (good-is-up);
  `-` = same but **inverted** (good-is-down). Use `|+%` for "more is better" stats,
  `|-%` for "less is better" (e.g. the Broken Mojave resistance/consumer-goods tooltips).
- `§H` bold, `§Y` yellow, `§!` reset. Prefer letting `|+%` / `|-%` color the number rather
  than wrapping it in `§R…§!`.

`*_var_tt` tooltips live with the other variable tooltips in `tnkd_idea_l_english.yml`.

## Tooltip spacing helper

`newline = yes` inserts a blank line into a tooltip (e.g. between effects in a focus
`completion_reward`):
```
# tnkd_scripted_effect.txt
newline = { custom_effect_tooltip = newline_tt }
# tnkd_idea_l_english.yml
newline_tt:0 "\n"
```
If a lone `\n` collapses in-game, bump the loc value to `"\n\n"`.

## Naming conventions

| Thing | Pattern | Example |
|---|---|---|
| Mechanic variable | `tnk_<name>_var` / `tnk_<name>_<stat>_var` | `tnk_decohesion_org_var` |
| Change temp var | `<var>_change` | `tnk_decohesion_org_var_change` |
| Update effect | `update_<var>` | `update_tnk_decohesion_org_var` |
| Tooltip loc key | `<var>_tt` | `tnk_decohesion_org_var_tt` |
| Dynamic modifier | `tnk_<name>_dynamic_modifier` | `tnk_brain_drain_dynamic_modifier` |
| Idea / modifier sprite | `GFX_idea_tnkd_<name>` | `GFX_idea_tnkd_fractured_mind` |
| Events | `nf_tnk.<n>` | `nf_tnk.33` |

## HOI4 / tooling gotchas
- `add_to_variable` / `subtract_from_variable` treat a missing variable as `0`, so
  `subtract_from_variable 0.02` ≡ `add_to_variable -0.02`.
- `add_dynamic_modifier` **stacks**; guard with `NOT = { has_dynamic_modifier { … } }` if it
  can be reached more than once.
- **CWtools `CW266` "command … does not exist in data type None"** on localisation lines
  that use `[?variable]` or scope tags (`TNK`, `VEG`, …) are **false positives** — the static
  analyzer cannot resolve script-defined variables / scopes from a context-free loc string.
  The whole mod relies on these; ignore them. A genuinely broken key produces different errors.

## Extending
To add a new dynamic-modifier mechanic, follow the established shape:
1. Define `tnk_<name>_dynamic_modifier` in `tnkd_dynamic_modifiers.txt` with an `icon` and
   variable mappings.
2. Choose an init strategy: a one-time event (preferred when there's a clear trigger) or a
   self-adding guarded `update_` effect like Brain Drain.
3. Add `update_tnk_<name>_var` in `tnkd_scripted_effect.txt` and a `*_var_tt` tooltip in
   `tnkd_idea_l_english.yml`.
4. Drive it from focuses / decisions via `set_temp_variable = { ..._change = X }` +
   `update_tnk_<name>_var = yes`.
