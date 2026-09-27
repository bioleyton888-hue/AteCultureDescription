# After the End: Culture Descriptions

A submod for **[After the End](https://steamcommunity.com/sharedfiles/filedetails/?id=3192256710)** (Crusader Kings III 1.19) that gives every culture a short narrative description, the way faiths already have one.

Hover over any culture, anywhere in the game (a character, a county, the cultures map mode), and the tooltip tells you who they are, where they came from and what makes them tick.

<p align="center">
  <img src="docs/tooltip_base_generic.png" alt="The Ruteno culture tooltip with its description" width="435">
</p>

**Steam Workshop:** https://steamcommunity.com/sharedfiles/filedetails/?id=3809203832

## Features

- **A description in every culture tooltip.** It appears below the usual content and never stretches the tooltip.
- **No culture is ever left blank.** ATE has 422 cultures. Those without a written description get a generic one based on their region: the South, the Caribbean, the Andes, the Arctic and so on.
- **New cultures included.** Hybrid and divergent cultures created during the game, by you or by the AI, get a description that fits how they were born.
- **Written descriptions win.** When a culture gets its own text, it replaces the generic one automatically, even in saved games.
- **Safe for existing saves.** You can add the mod to a running campaign: every culture gets its description by the next January 1st.
- **Community-driven.** Every description ends with an invitation to write a better one (see below).

## Help write the descriptions

There are 422 cultures in After the End, and each deserves someone who actually loves it. The descriptions are being written by the community in a shared spreadsheet:

**👉 [After the End culture descriptions (Google Sheets)](https://docs.google.com/spreadsheets/d/1l1CXGpG0bm9sCHKsWecNKvg0Pni-3jLZ94Ml3ykzkDM/edit)**

1. Find your favorite culture. They are grouped by region and heritage.
2. Write its description in the **Description** column.
3. Put your name in **Written by** to get credit.

**Rules:**

- English, **2 to 4 sentences**. It has to fit in a tooltip.
- Keep the ATE flavor: post-apocalyptic America, where the institutions of the old world turned into myths.
- No line breaks, and no `[ ]`, `$` or `#` characters (the game reads them as code).
- In the **Generic descriptions** tab, write `{culture}` wherever the name of the new culture should go.
- Don't edit or delete other people's entries.

## Requirements and compatibility

- **Crusader Kings III 1.19** and **After the End**. Load this mod **after** After the End.
- The mod replaces `gui/shared/cooltip.gui` (the shared tooltip file) to add the description. It is **incompatible with other mods that change that file**: whichever loads last wins. After the End itself does not touch it.
- Game mechanics are untouched: no changes to cultures, traditions, hybridization or divergence.

## How it works

For anyone curious, or anyone writing a compatibility patch:

- Each culture stores its description key in a **culture variable** (`ate_cd_culture_key`). The tooltip turns it into a localization key and shows that text. Because the key lives in the culture and not in its name, the description survives renames and is saved with the game. The technique comes from **[Elder Kings 2](https://steamcommunity.com/sharedfiles/filedetails/?id=2887120253)**.
- **Written descriptions** are registered at game start and again on every `yearly_global_pulse`, so saved games pick up descriptions added in later versions.
- **Generic descriptions:** ATE's 106 heritages are grouped into **15 regional families**. Each family has three texts:
  - **base**, for an original ATE culture without its own text;
  - **hybrid**, for a culture born from blending two cultures;
  - **divergent**, for a culture that broke away from its parent.

  New cultures get theirs through `on_culture_created`.
- A culture with no family (for example, one from another mod) shows a **universal fallback** text.

| Family | Heritages |
|---|---|
| Southern US | Floridian, Deep South, Old Southern, Louisianais, Geechee, Upland, Texan |
| Northeast & Great Lakes | Mid-Atlantic, Yankee, Lakelander, Midwestern, Northlake, Amish, Mennonite, Shtetl |
| Western US | Californian, Frontieran, Rockland, Transcascadian |
| Canada | Canayen, Laurentian, Atlantic Canadian, Prairie |
| Native peoples of the East & the Plains | Plains, Sioux, Sequoyan, Anishinaabe, Wabanaki, Longhouse, Lumbee, Seminole |
| Native peoples of the West & the Pacific | Plateau, Great Basin, Interior, Columbian, Lower Coast, Upper Coast, Native Californian |
| North & Arctic | Arctic, Dene, Boreal, East-Cree, Peninsular, Spitsberger |
| Mexico | Normexa, Centromexa, Sudmexa, Mexonite |
| Native Mesoamerica | Mesoamerican, Maya, Tehuantepec, Aridomex |
| Central America | Centroamericano, Honduran, Panamanian, Mesocreole |
| Caribbean & the Guianas | Greater Antillean, Lesser Antillean, Guyanan, Maroon, Cayennais, Native Guyanan |
| Andes & northern South America | Colombian, Native Colombian, Venezuelan, Llanero, Ecuatoriano, Andino, Bajo Peruano, Nazco, Boliviano |
| Brazil | Cabano, Café com Leite, Costeiro, Geraizeiro, Manguetowner, North-Sertanejo, West-Sertanejo, Paulistânico, Serra do Mar, Sulista, Cíclico |
| Amazonia & native Brazil | South-Amazonian, Upper-Amazonian, Japurá-Negro, Madeira-Tapajós, Roraiman, Tupian, Selvático, Orinocoan, Northern Jê, A'uwê Jê, Southern Jê, Cariri |
| Southern Cone | Chileno, Gaucho, Platense, Viejispano, Patagonian, Austral, Mapuche, Islander, Guaraní, Chacoan, Guaicurú |

## Project layout

```
common/
  on_action/          game start, yearly pulse and culture creation hooks
  scripted_effects/   key assignment; *_generated.txt files are generated, do not edit by hand
  script_values/      lets the tooltip check whether a culture has a description
gui/shared/cooltip.gui   vanilla tooltip file + the block marked #ATE_CD ADDITION
localization/english/    descriptions (per heritage), generics and the fallback
prds/, plans/            design documents (in Spanish)
docs/                    screenshots
```

All `.txt`, `.yml` and `.gui` files are UTF-8 **with BOM** and use real tab characters, as CK3 requires. `.gitattributes` disables line-ending conversion so the files stay byte-identical.

## Credits

- **After the End** team, for the world these cultures live in.
- **Elder Kings 2** team, for the culture-description technique.
- Every community member who writes a description: your name goes in the credits.
