# RPG Equipment & Durability

A SillyTavern extension that gives the player a worn **outfit** across six slots, each with a **durability** bar that wears down as the story goes on and eventually **breaks**, plus a **4‑tier quality grade** on weapons and armour. The current outfit (condition + grade) is quietly injected into the prompt each turn, so the character can *see* what you're wearing — a torn coat, broken boots, a **legendary** blade.

> Design principle: **the extension is the source of truth, the chat is just the narrator.** Durability and grade are computed by the extension — the model never does the math, it only reads a short state note. Part of the RPG suite: it exposes `window.RPG.equipment` (RPG Vitals reads it for armour/attack; RPG Vendors uses it to sharpen and repair) and can pull repair materials from `window.RPG.inventory`.

**Version 1.11.1**

---

## ✨ Features

- 🧥 **Six slots** — Head · Top · Bottom · Boots · Accessory · Weapon — on a "case‑file / dossier" card.
- 📉 **Durability** — each piece has a bar (current / max) and a state word: *good → worn → tattered → BROKEN*. A broken item stays equipped (shown as damaged) so the scene can react.
- ⭐ **Quality grades (1–4)** — every weapon/armour piece has a grade: **Worn → Honed → Fine → Legendary**, which **multiplies** its attack/armour (×1 / ×1.4 / ×1.9 / ×2.6). Grade shows as coloured stars in the slot caption and a full line in the detail card, plus a coloured **glow** (grey/blue/purple/gold) with a gentle pulse and a **gold shimmer** on Legendary.
- 🎲 **Grades arrive at random** — gear that enters the world rolls a weighted grade: Legendary is only ~3%. You mostly reach Legendary by **sharpening** (in the Vendors module), not by luck.
- 🧱 **Start broken (optional, on by default)** — new gear arrives broken and needs repair; toggle it off in settings if you'd rather start intact.
- 💥 **Shatter can cost a tier** — when a piece breaks in combat/wear, there's a chance its grade drops one step ("градация случайно выпадает").
- ⚙️ **Steady decay** — every *N* messages each worn piece loses durability; at 0 it breaks. With **AI wear** on, an extra hit lands on the *specific* garment the scene damaged.
- 🚫 **Slot‑type guard** — you can't wear a weapon as a hat, an accessory on the torso, or eat‑ables as armour; sensible clothing/armour goes anywhere on the body (fine cases handled by an optional AI check).
- 🩹 **Field patch** & 🛠️ **Edit mode** — improvised low‑chance repair, and full manual control (repair, clear, add by hand with a starting durability).
- 👕 **Auto‑outfit from your description** — one button dresses the player in setting‑appropriate gear pulled from your **Persona description** (with fallbacks); grade is assigned right away.
- ⚔️🛡️ **Stats** — armour mitigates incoming damage and the weapon adds attack (grade‑scaled); these feed RPG Vitals when present.
- 🧠 **Context injection** — a compact note like `[{{user}}'s outfit — Top: Leather coat (worn); Weapon: Sabre (atk 12) [Legendary]. {{char}} can see this.]`.
- 🌍 **Bilingual (RU / EN)**; state saved per chat.

## 📦 Install

Copy the `RPG-Equipment-Durability` folder into your third‑party extensions folder (e.g. `SillyTavern/data/<user>/extensions/`), reload, and enable it in **Extensions → RPG Equipment (Outfit & Durability)**.

## ⚙️ Setup

1. Tick **Enable Equipment** and pick a **Language**.
2. Fill in an OpenAI‑compatible **URL / API Key / Model** (default `google/gemma-4-31b-it`) — used for auto‑outfit and the optional wear/equip AI checks.
3. Tune **Durability drops every (messages)** and **Durability lost each time**, the **field‑patch** chance, the **injection depth**, and the **New gear starts broken** toggle (default on).

A shirt button appears on the right side to open the panel.

## ⭐ Grades & sharpening

Grade lives on the item and is **preserved when you unequip and re‑equip** (it no longer re‑rolls). To raise a grade, use the **Vendors module**: a blacksmith (weapons) or tailor (armour) sells **⚒ sharpening recipes** (→Honed / →Fine / →Legendary), each with its own ingredients. Sharpening bumps the grade one tier and restores durability; reaching **Legendary (3→4) can fail** and burns the materials. Vendors' **🛠 repair kits** restore durability by grade tier. Sharpening/repair works on both **equipped** and **backpack** gear.

## 🧠 How it works

Every *N* messages each equipped piece loses durability; at 0 it breaks (stays equipped, shown broken, maybe a tier lower). The current outfit — condition **and grade** — is injected each turn so the character narrates around it. If armour stats are on, worn pieces reduce incoming HP damage in RPG Vitals and the weapon sets your (grade‑scaled) attack.

## 🔌 Cross‑extension bridge

Exposes `window.RPG.equipment`: `isEnabled()`, `list()` (includes `grade` / `gradeName`), `sharpenable()`, `getGrade(slot)`, `sharpen(slot)`, `repairable()`, `repair(slot, amount)`, plus armour/attack totals consumed by RPG Vitals. Reads `window.RPG.inventory` to repair with backpack materials when present.

## 🩺 Troubleshooting

- **Quality changed when I re‑equipped a piece.** Fixed in 1.11.0 — grade is stored on the item and kept through unequip/equip.
- **I couldn't put shoes in Boots / a dress in Top.** Fixed in 1.11.1 — the slot‑type guard now only blocks clearly‑wrong types (weapon/accessory/food); ordinary clothing/`misc` goes on the body.
- **"Outfit from my description" did nothing.** Fixed in 1.11.0 — it now reads your Persona description (with fallbacks) and warns clearly if there's no description to use.
- **Durability barely moved.** Fixed in 1.11.0 — a steady baseline wear now always applies (AI wear stacks on top); tune the interval/amount in settings.
- **Grade shows only after re‑equipping.** Fixed in 1.10.1 — grade (with stars + a detail line) shows immediately; grade 1 is subtle by design.
- **Auto‑outfit / AI checks do nothing.** They need a working URL / key / model.
