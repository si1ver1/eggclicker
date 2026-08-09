# 🥚 Egg Empire

A cozy incremental clicker game that runs entirely in your browser. Crack eggs, separate the yolks from the whites, sell both streams for cash, and reinvest in workers, machines, and factories until the whole operation runs itself.

Built as a single self-contained HTML file — no dependencies, no build step, no server required.

## How to play

Open `index.html` in any modern browser (double-clicking it is enough). Because it's named `index.html`, any static host or web server will also serve it automatically as the site's front page.

The production line runs top to bottom:

1. **Crack station** — tap/click the egg to crack it. Cracked eggs drop down into the separator queue.
2. **Separator** — tap/click the bowl to split each cracked egg into one yolk and one white.
3. **Tanks** — yolks and whites collect in their own tanks and **sell automatically** every second. Yolks are worth more; whites sell just as fast.

Money appears in the sticky header, along with your current income per second and lifetime eggs processed.

## Upgrades

The shop has three sections:

| Section | What it does |
|---|---|
| **Hands & hardware** | Automation. Farmhands and Crack-o-Matics crack eggs for you; Kitchen apprentices and Spin separators handle separation; Egg factories do the entire line at 25 eggs/sec. Buy as many of each as you can afford — prices rise ~15% per purchase. |
| **Station skills** | Click power. Stronger wrists (+1 crack per click) and Deft hands (+1 separation per click). |
| **Product lines** | Per-component boosts. Golden yolks / Cloud whites raise the sale price ×1.5 per level; the Yolk and Whites pumps double how fast each tank sells; the Bigger vat upgrades double a tank's capacity. |
| **Sell the farm** | Prestige. Once you've earned $150k in a run, sell the farm for **nest eggs** (more earnings = more nest eggs, on a square-root curve). Each nest egg permanently boosts all sale prices by +25%. Selling resets money, crew, and upgrades — nest eggs and lifetime stats persist, the background palette shifts, and your nest-egg count is watermarked behind the play area. |

### Levels — hatch a new world

Selling the farm 5 times unlocks **Level up** (bottom of the shop). Leveling resets *everything* — money, upgrades, nest eggs, milestones — like starting a new game, but you keep a permanent ×1.5 price multiplier per level and the whole game re-themes:

1. 🌾 Cozy farm → 2. 🍄 Underground hencave → 3. 🕹️ Retro arcade → 4. 🚀 Deep space station → 5. 👑 Golden dimension → (cycle repeats)

Your current level shows in the header and as a watermark behind the play area. Each world redraws the click stations — a speckled cave egg and stone bowl underground, a chunky pixel egg and neon bowl in the arcade, a starry cosmic egg with an orbit ring and a UFO bowl in space, and a crowned golden egg with a gem-studded chalice in the golden dimension. Each world also reskins the whole crew — e.g. the hencave replaces the Freight truck with an 🚜 Excavator and the Farmhand with a ⛏️ Mole miner; space runs on 🧑‍🚀 cadets and 🚀 freight rockets. Same stats and prices, new flavor. The prestige requirement is the `LEVEL_REQ` constant; themes and their crew skins live in the `THEMES` array.

### Events & milestones

- **Golden eggs** appear at a random spot every few minutes (first one within ~90 seconds) and last 12 seconds. Clicking one gives either an instant cash windfall or a **frenzy** — all prices ×3 for 20 seconds.
- **Milestones** at 250 / 1k / 5k / 20k / 100k / 500k / 2.5M lifetime eggs each permanently add +5% to prices (compounding), with a celebration toast. Milestones survive prestige.

### Tanks, capacity, and pressure

- Each tank holds a limited amount (base 150). When either tank is full, the separator stalls and cracked eggs pile up in the queue — buy Bigger vats and pumps to keep the line moving.
- **Pressure bonus**: a tank that's over 90% full sells at ×2 speed (shown as ⚡ in the tank meta). Riding near the limit is rewarded, not just punished.
- **Delivery trucks** (Hands & hardware): vans and freight trucks add flat sell speed to *both* tanks on top of the pumps — a second way to scale throughput.
- **Overflow valves** (one-time, per tank): once installed, a full tank never stalls the separator — overflow sells instantly at 50% price. The line keeps moving; you just pay an efficiency tax, softened by the pressure bonus running constantly at full.

Tip: automation is only as fast as its slowest stage — a fleet of farmhands with nobody separating just piles up cracked eggs, and a full tank stalls everything upstream.

## Saving & offline earnings

- Progress autosaves to the browser's `localStorage` every 5 seconds (and when you close or hide the tab).
- While the page is closed, your crew keeps working at **50% efficiency, capped at 8 hours**. The payout is applied (with a welcome-back toast) the next time you open the game.
- Saves are per browser and per device. **Reset progress** is in the page footer.

## Mobile

The layout adapts to phones: a compact sticky header, a single-column production line that fits one screen, and touch-tuned tap targets (no double-tap-zoom delay). The upgrade shop becomes a bottom drawer — tap the **Upgrades** bar to expand it; a green badge on the bar shows how many items you can currently afford. Shop sections are individually collapsible on all screen sizes. Just open the same file on a mobile browser.

## Development

Everything lives in `index.html`:

- **CSS** — cozy-farm theme variables at the top of the `<style>` block (`--cream`, `--yolk`, etc.), mobile styles in the `@media (max-width:700px)` block at the bottom of it.
- **Game balance** — the `HIRES` and `UPGRADES` arrays near the top of the `<script>` define every purchasable item (base cost, cost multiplier, rates). The derived-stat helpers just below them (`yolkPrice`, `crackPerClick`, …) hold the pricing formulas.
- **Animations** — `flyDrop()` (droplets between stations, via the Web Animations API), `shellPop()` (shell shards), and the `wobble` / `tilt` / `cracking` CSS classes.
- **Game loop** — `tick()` runs on `requestAnimationFrame`; `load()` handles save migration and the offline-earnings calculation.

To preview with live reloads during development, any static server works, e.g.:

```bash
python -m http.server 8137
```

then browse to `http://localhost:8137/`. (`.claude/launch.json` is preconfigured to launch exactly that for Claude Code's browser preview.)

Save data lives under the `eggEmpireSave_v1` key — bump the key name if you make a breaking change to the save format.
