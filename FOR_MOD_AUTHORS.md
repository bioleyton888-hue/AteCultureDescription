# Adding a description for your mod's culture

A step-by-step guide for mod authors who add cultures to **After the End** and want
**[After the End: Culture Descriptions](https://steamcommunity.com/sharedfiles/filedetails/?id=3809203832)**
to show a description for them in the culture tooltip and the culture window.

It takes **three small files** in your own mod and no changes to this one. The worked
example throughout is the Neomoor culture of the *Neomoor Culture Revival* mod, which does
exactly this.

> **Is this guide for you?** Only if the culture is **defined by your mod**. To write a
> description for one of ATE's own 422 cultures, use the
> [community spreadsheet](https://docs.google.com/spreadsheets/d/1l1CXGpG0bm9sCHKsWecNKvg0Pni-3jLZ94Ml3ykzkDM/edit)
> instead (see the main [README](README.md#help-write-the-descriptions)).

---

## How it works in one paragraph

Every culture can carry a **culture variable** called `ate_cd_culture_key` whose value is a
flag, for example `flag:ate_cd_neomoor`. The interface of this mod reads that flag, adds
`_desc` to its name and shows the localization key `ate_cd_neomoor_desc`. So your mod only
has to **write the text** and **set the variable**. That's the whole contract.

It is a **soft dependency**: if a player runs your mod without this one, nothing reads the
variable or the key, nothing is shown and nothing breaks. You don't need to list this mod as
a requirement.

---

## Before you start

Pick one name and use it everywhere. In this guide, `<culture>` is your culture's id (the
name of its block in `common/culture/cultures/`), and `<prefix>` is your mod's usual prefix
for effect and file names.

| Thing | Pattern | Neomoor |
|---|---|---|
| Culture id | `<culture>` | `neomoor` |
| Flag | `ate_cd_<culture>` | `ate_cd_neomoor` |
| Localization key | `ate_cd_<culture>_desc` | `ate_cd_neomoor_desc` |
| Your effect | `<prefix>_set_culture_description_effect` | `ate_neomoor_set_culture_description_effect` |

The flag and the key must match: the key is always **the flag + `_desc`**.

---

## Step 1 · Write the description

Create `localization/english/<prefix>_culture_description_l_english.yml`:

```yaml
l_english:
 # Shown by the AteCultureDescription mod (culture tooltip and culture window)
 ate_cd_<culture>_desc:0 "Two to four sentences about the culture."
```

Neomoor's, for reference
(`localization/english/ate_neomoor_culture_description_l_english.yml`):

```yaml
l_english:
 # Shown by the AteCultureDescription mod (culture tooltip and culture window)
 ate_cd_neomoor_desc:0 "Coming from a long line of Motowner Muslim exiles from Detroit, these Sunshiner descendants took in all the heritage, stories, tales and religion of those people and blended them with their own mythos. Out of it they forged a new identity in the swamps of Florida that looks like a distortion or parody of both its roots. They believe they are the Moors of the old tales, and behave like it to the best of their abilities."
```

Writing rules:

- **2 to 4 sentences.** It has to fit in a tooltip. Long texts wrap, they never stretch the
  tooltip, but a wall of text is hard to read on hover.
- **No line breaks.** Avoid `[ ]`, `$` and `#`: CK3 reads them as code in localization.
- The file must be **UTF-8 with BOM** and its name must end in `_l_english.yml`, or CK3
  ignores it silently.
- This mod is English only. If your mod is translated, you can add the same key to your
  other `l_<language>` files.

---

## Step 2 · Point the culture at the text

Create `common/scripted_effects/<prefix>_culture_description_effects.txt`:

```
<prefix>_set_culture_description_effect = {
	culture:<culture> = {
		# 1. The key: the interface shows the loc key "ate_cd_<culture>_desc"
		set_variable = {
			name = ate_cd_culture_key
			value = flag:ate_cd_<culture>
		}
		# 2. "This is a real description, not a regional generic you may replace"
		if = {
			limit = { has_variable = ate_cd_key_is_base_generic }
			remove_variable = ate_cd_key_is_base_generic
		}
		# 3. Optional: hides the "write a better one" invitation in the culture window
		set_variable = ate_cd_has_written_text
		# 4. Empty check that only "uses" the flag: the interface reads it, but CK3
		#    only counts script uses and would warn "flag set but never used"
		if = {
			limit = { var:ate_cd_culture_key = flag:ate_cd_<culture> }
		}
	}
}
```

What each part does:

1. **The key.** This is the only line that makes the description appear.
2. **The base-generic mark.** Until your effect runs, this mod may already have given your
   culture a regional generic text (see *Why the order doesn't matter* below) and marked it
   with `ate_cd_key_is_base_generic`. That mark means "replace me if a real text shows up".
   Removing it keeps this mod from ever overwriting your key.
3. **The invitation line.** Cultures without their own text show a line in the culture
   window asking players to write a better description in the community spreadsheet. Your
   culture has a real one, so hide it.
4. **The anti-warning.** Without it, `error.log` shows
   `Flag ate_cd_<culture> is set but is never used` every time the effect runs.

Use a tab for each indentation level (not spaces) and save the file as UTF-8 with BOM.

---

## Step 3 · Run it at game start and every year

Create `common/on_action/<prefix>_culture_description_on_action.txt`:

```
on_game_start = {
	on_actions = { <prefix>_culture_description }
}

yearly_global_pulse = {
	on_actions = { <prefix>_culture_description }
}

<prefix>_culture_description = {
	effect = {
		<prefix>_set_culture_description_effect = yes
	}
}
```

- `on_game_start` covers **new games**.
- `yearly_global_pulse` covers **saved games**: CK3 has no "on load" hook, so a player who
  adds your mod (or this one) to a running campaign sees the description by the next
  January 1st. This mod uses the same timing.
- On_actions **merge across files**: adding to `on_game_start` and `yearly_global_pulse`
  like this does not override vanilla, ATE or anyone else. No file copying is needed.
- The effect is **idempotent**: running it every year only sets the same values again.

That's all. Your mod now has three new files and nothing else changed.

---

## Test it

1. Launch with **After the End**, your mod and this mod enabled, and start a new game.
2. Hover your culture anywhere (a character, a county, the cultures map mode). The
   description appears at the bottom of the tooltip, under a divider.
3. Open the culture window, *Traditions and Pillars* tab: the same text sits under the ethos
   banner, **without** the "write a better one" line if you kept step 2.3.
4. **Saved game:** load a save made before you added the code. Either wait until January 1st
   or, with the game in debug mode, run the effect from the console:
   ```
   effect <prefix>_set_culture_description_effect = yes
   ```
5. Check `Documents/Paradox Interactive/Crusader Kings III/logs/error.log` for your flag, key
   and file names. It should say nothing about them.

| You see | Cause |
|---|---|
| A regional generic text instead of yours | The effect has not run yet (saved game: wait for Jan 1st or use the console), or the effect name in the on_action is misspelled |
| The raw key `ate_cd_<culture>_desc` | The loc key is missing or misspelled, or the `.yml` has no BOM / wrong name and CK3 skipped it |
| `Flag ... is set but is never used` in `error.log` | Step 2.4 is missing or uses a different flag |
| Nothing at all, not even a generic | This mod is not enabled, or another mod that replaces `gui/shared/cooltip.gui` or `gui/window_culture.gui` loads after it |

---

## Good to know

**Why the order doesn't matter.** Both mods run on the same on_actions, and CK3 does not
guarantee which runs first. It doesn't matter:

- If **this mod runs first**, it gives your culture the generic of its heritage family and
  marks it. Then your effect overwrites the key and removes the mark.
- If **your mod runs first**, your culture already has a key without the mark, so this mod
  leaves it alone.

**A text written by a player still wins.** If a culture head uses the *Edit Description*
button on your culture, their text takes priority over yours. Your effect doesn't touch the
player's text, so it survives the yearly refresh.

**Hybrid and divergent cultures born from yours** get a regional generic text from this mod
at creation, as any other new culture does. That only works if your culture uses one of
ATE's heritages. If your mod adds a **new heritage**, this mod doesn't know which region it
belongs to, and its new cultures show a universal fallback text.

**Only reference cultures your mod defines.** `culture:<culture>` must exist in every game
where your file loads. Pointing at a culture from a third mod logs errors whenever that mod
is not enabled.

**Several cultures?** Repeat the `culture:... = { ... }` block inside the same effect, one
per culture, and add one loc key for each. One effect and one on_action are enough.

---

## Checklist

- [ ] `ate_cd_<culture>_desc` in a `_l_english.yml` file with BOM, 2–4 sentences
- [ ] Effect sets `ate_cd_culture_key = flag:ate_cd_<culture>` and removes
      `ate_cd_key_is_base_generic`
- [ ] Optional: `set_variable = ate_cd_has_written_text`
- [ ] Anti-warning `if` with the same flag
- [ ] Effect hooked to `on_game_start` **and** `yearly_global_pulse`
- [ ] Tested in a new game, a saved game, and `error.log` is clean

Questions or a mod that uses this? Open an issue on
[GitHub](https://github.com/bioleyton888-hue/AteCultureDescription) or comment on the
[Workshop page](https://steamcommunity.com/sharedfiles/filedetails/?id=3809203832). It's
also worth crediting this mod in your Workshop description so players know to enable it.
