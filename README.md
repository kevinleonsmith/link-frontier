# Link Frontier

A browser-based strategy sim where you build a blockchain-themed frontier empire — gather
resources, trade on a live market, stake and compete — solo or with others.

> **Simulated economy.** “LINK” and every balance in this game are in-game play currency.
> Link Frontier involves **no real cryptocurrency, token, wallet, blockchain transaction, or
> money**, and is an **independent fan simulation — not affiliated with, sponsored by, or
> endorsed by Chainlink or SmartContract Chainlink Limited**.

## Gameplay

- **Resource chains** — Lumber Camps, Quarries, Farms and Gold Mines produce wood, stone, food
  and gold; production ticks every second and is capped by Warehouses you build and upgrade.
- **Land & buildings** — start on a few plots, buy more with LINK, and place buildings (resource
  camps, Houses, Warehouses, Marketplaces, a Barracks and a Staking Vault) with their own upgrade paths.
- **Ages** — advance your empire from the Dark Age through Feudal, Castle and Imperial. Each age
  costs resources, unlocks new buildings, units and technologies, and buffs all production.
- **Villagers & population** — raise villagers with food and assign them to gather wood, stone,
  gold or food on top of your buildings. Houses raise the population cap that villagers and
  soldiers share.
- **Technology** — a research tree of permanent bonuses (gathering, storage, army attack/defense,
  battle loot, trade), unlocked age by age.
- **Military & war** — train Militia, Archers, Knights and Cannons at the Barracks, then **raid AI
  war-bands** for loot or launch **PvP raids** against other real empires for a cut of their LINK.
  Losing a battle costs you casualties; being raided triggers a 10-minute defensive shield.
- **Dynamic market** — buy and sell wood, stone and gold for LINK at prices that move with
  supply and demand; big sells push the price down before it drifts back.
- **Staking** — build a Vault and stake LINK for live yield.
- **Contracts** — one-time milestone quests with rewards.
- **Alliances (multiplayer)** — found or join an alliance, pool a shared treasury for
  empire-wide production buffs, chat in real time, and climb the leaderboards.
- **Treasury seasons** — a rolling 7-day competition: alliances rank by treasury contributed
  that season, with prizes for the top alliances plus an individual MVP bonus.

## Two ways to run it

Link Frontier is a single self-contained `index.html` file, and how much of it works depends on
where you open it:

| Where | Single-player | Multiplayer (alliances, chat, leaderboards, seasons) |
|-------|:-------------:|:----------------------------------------------------:|
| **claude.ai Artifact** (the hosted version) | ✅ | ✅ |
| **Opened locally / GitHub Pages / any static host** | ✅ | ❌ (runs solo) |

The multiplayer layer relies on the claude.ai Artifact runtime for its shared database and
player identity. Outside that runtime those APIs simply aren't present, so the game detects
their absence and runs as a fully playable **single-player** experience — no errors, just no
shared world.

> **Enabling multiplayer on claude.ai.** The `db` and `user` capabilities are granted at
> **publish time**, not in the HTML — so publishing `index.html` as-is leaves them undeclared and
> the game silently runs solo. Publish it with these capabilities (everyone reads, each player
> writes only their own `{self}` documents):
>
> ```json
> {
>   "db": { "rules": [
>     { "path": "empires",        "read": "view", "write": "owner" }, { "path": "empires/{self}",   "write": "interact" },
>     { "path": "alliances",      "read": "view", "write": "owner" }, { "path": "alliances/{self}", "write": "interact" },
>     { "path": "chatmsgs",       "read": "view", "write": "owner" }, { "path": "chatmsgs/{self}",  "write": "interact" },
>     { "path": "mod",            "read": "view", "write": "owner" }, { "path": "mod/{self}",       "write": "interact" },
>     { "path": "raids",          "read": "view", "write": "owner" }, { "path": "raids/{self}",     "write": "interact" }
>   ] },
>   "user": {}
> }
> ```
>
> Writing shared data also needs the viewer to be a **Contributor or above**: the owner and
> email-invited editors always can, and on a Team/Enterprise plan so can org members who can open
> the artifact. View-only visitors (and, on a personal plan, public-link strangers) can watch the
> leaderboards but can't join alliances, contribute, or raid — so cross-account PvP needs a shared
> org or explicit editor access.

### Run locally

Open `index.html` in any modern browser (double-click it, or serve the folder):

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Progress is saved in your browser's local storage.

## Architecture notes

- **Vanilla HTML/CSS/JS, no build step, no dependencies.** One file, one game loop.
- **Single-player state** lives in `localStorage`.
- **Multiplayer state** (when running as a claude.ai Artifact) uses the Artifact `db` + `user`
  capabilities. Every write is locked to the writer with per-viewer `{self}` access rules:
  - `empires/{self}` — leaderboard entry, alliance membership and contributions.
  - `alliances/{self}` — an alliance document whose id **is** its founder's user id, so the
    founder can't be reassigned and only the founder can edit it.
  - `chatmsgs/{self}` — each member's own chat messages (so you can only delete your own).
  - `mod/{self}` — a founder's hidden-message list for moderation; clients trust only the
    founder's list.
  - `data/users/<self>/seasonclaims` — a private, lease-guarded record so season prizes can't
    be double-claimed.

## License

[MIT](LICENSE)
