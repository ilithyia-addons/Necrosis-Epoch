# Necrosis — Project Epoch Edition

<p align="center">
  <img src="UI/Necrosis.tga" alt="Necrosis" width="96" />
</p>

<p align="center">
  <strong>The classic Warlock UI &amp; shard manager, polished for Project Epoch.</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-3.1.0-9b4dca?style=flat-square" alt="Version" />
  <img src="https://img.shields.io/badge/client-3.3.5%20WotLK-1f6feb?style=flat-square" alt="Client" />
  <img src="https://img.shields.io/badge/server-Project%20Epoch-7c5cff?style=flat-square" alt="Server" />
  <img src="https://img.shields.io/badge/license-GPL%20v2-d62828?style=flat-square" alt="License" />
</p>

---

## About

This is a fork of **Necrosis 3.0 (r226)** — the iconic Warlock companion addon originally crafted by **Lomig** and maintained by **Tarcalion** — adapted for the **Project Epoch** server.

Project Epoch ships with a few quality-of-life tweaks that diverge from retail behaviour (e.g. soul shards stacking by 3 instead of 1). This fork patches Necrosis so that its long-standing automation features keep working correctly under those rules.

## Features

### Soul Shard Management
- **Automatic shard sorting** — keeps your shards tidy in your designated soul pouch.
- **Maximum-shards-to-keep** — destroys excess shards above a configurable threshold (now correctly aware of stack sizes).
- **Visual shard counter** on the main sphere with skinned textures.

### Stones at a Glance
- One-click access to **Soulstone**, **Healthstone**, **Spellstone**, **Firestone**, and **Hearthstone** with cooldown tracking.
- Smart in-combat / out-of-combat item swapping.
- `Shift+Click` the Soulstone button to cast **Ritual of Summoning**.

### Spell &amp; Cooldown Tracking
- Graphical and text timers for **Corruption**, **Curses**, **Immolate**, **Banish**, **Fear**, **Enslave Demon**, **Soulstone Resurrection**, **Soulshatter**, **Fel Domination**, **Infernal**, and more.
- Customizable timer position, scale, and styling.
- Audible alerts for **Backlash**, **Shadow Trance**, **Soulstone Expiry**, **Enslave end**, and **Fear breaks**.

### Demons &amp; Buttons
- Quick-summon menu for all your minions.
- Configurable spell button bar with full keybinding support.
- Mount selection helper (because warlocks have *opinions*).

### UI &amp; Customization
- Multiple **sphere skins** (Turquoise, Orange, Rose, Bleu, …).
- Locale support: enUS, deDE, frFR, esES, ruRU, zhCN, zhTW.
- Configurable message routing (chat / system / floating combat text).
- Slash command interface — type `/necrosis` to access settings.

## Installation

1. Close the WoW client.
2. Copy the `Necrosis` folder into:
   ```
   <Project Epoch>/Interface/AddOns/
   ```
3. Launch the game and ensure the addon is enabled at the character select screen.
4. In-game, type `/necrosis` to open the configuration panel.

## Changelog

### 3.1.0 — Project Epoch Edition

**Fixed: "Maximum number of shards to keep" destroys all shards on Project Epoch.**

On servers where soul shards stack (Project Epoch stacks them by 3), the auto-destroy logic was flawed in three independent ways:

1. **Whole-stack deletion.** `PickupContainerItem` + `DeleteCursorItem` always deleted the entire stack. With stacks of 3, every "delete one shard" operation actually removed 3, frequently overshooting and wiping the inventory.
2. **Async inventory desync.** `GetItemCount` was queried immediately after deletion, but the client doesn't reflect the change instantly — so the loop kept reading the *old* count and deleting again on subsequent bag passes.
3. **One-stack-per-bag cap.** The inner loop broke after processing the first shard stack found in each bag, capping deletions at 5 stacks total. With 20 shards stored in a single bag, only 3 would ever be removed.

The destroy routine now:

- Reads the actual stack size with `GetContainerItemInfo` and uses `SplitContainerItem` to peel off the exact number of excess shards from a stack.
- Tracks the running count locally instead of relying on `GetItemCount` after each delete.
- Continues iterating slots within a bag until the target shard count is reached.

Both call sites (`Necrosis.lua` `BagExplore` and `FindSlot`) were patched.

## License

Distributed under the **GNU General Public License v2.0**.
See [`LICENSE`](LICENSE) for the full text.

## Credits

- **Lomig** — original author of Necrosis LdC.
- **Tarcalion** *(Nagrand US/Oceanic)* — long-time maintainer (Necrosis 3.x).
- **Liadora**, **Nyx**, **Eliah**, **Ner'zhul**, **Geschan**, **DosS**, **Komsomolka**, **StingerSoft**, **oXid_FoX** — contributors and localizers.
