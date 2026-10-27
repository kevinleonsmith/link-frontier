# Link Frontier

A browser-based strategy sim where you build a blockchain-themed frontier empire — gather
resources, trade on a live market, stake and compete — solo or with others.

> **Simulated economy.** “LINK” and every balance in this game are in-game play currency.
> Link Frontier involves **no real cryptocurrency, token, wallet, blockchain transaction, or
> money**, and is an **independent fan simulation — not affiliated with, sponsored by, or
> endorsed by Chainlink or SmartContract Chainlink Limited**.

## Gameplay

- **Villagers & population** — train villagers at the Town Center and assign them to food, wood,
  stone or gold. Farms, Lumber Camps, Quarries and Mines double as drop sites that speed up the
  villagers working that resource. Houses raise the population cap.
- **Four ages** — advance from the Dark Age through the Feudal and Castle Ages to the Imperial Age.
  Each age unlocks new buildings, units and technologies (Wheelbarrow, Loom, Forging, Masonry…).
- **Armies with counters** — Barracks, Archery Range and Stable train six unit types in a
  rock-paper-scissors system: spears beat cavalry, archers beat infantry, cavalry runs down archers.
- **Raids & campaign** — from the Feudal Age, raiders attack your town (Watch Towers help defend).
  You can also march on enemy camps for spoils. Raids only advance while the game is open, and
  there's a Peaceful mode toggle.
- **Wonder** — in the Imperial Age, raise a Wonder as the crowning monument.
- **Resource chains** — Lumber Camps feed Quarries feed Gold Mines; production ticks every
  second and is capped by Warehouses you build and upgrade.
- **Land & buildings** — start on a few plots, buy more with LINK, and place six building types
  with their own upgrade paths.
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
