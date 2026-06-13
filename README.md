# Star Citizen CoPilot (SC CoPilot)

SC CoPilot is a desktop companion for Star Citizen — a copilot that runs
alongside the game and handles the busywork while you play. It reads the game's
log live, so it keeps up with what you're doing without you having to touch it,
and surfaces what's useful on a second screen or an overlay.

Hauling is its first focus: tracking your cargo contracts, planning an efficient
route between stops, laying out your ship's cargo bay, and keeping a running
balance. But it's built to grow into a general Star Citizen copilot — hauling is
where it started, not where it stops, and more tools are on the way.

It runs entirely on your machine. No account, no launcher, nothing to configure
to get going.

## Download

**The latest build is available now on the
[Releases page](https://github.com/Maxybo/SC-CoPilot/releases/latest)** — download
the single `SCCoPilot.exe`. No installer, no dependencies.

The exe isn't code-signed, so Windows SmartScreen may warn you the first time you
run it. Click "More info", then "Run anyway". That warning is normal for small
independent apps (signing certificates are expensive).

The app keeps itself up to date: when a new release is out it offers to download
and install it, and you can check manually under Settings > Update.

### Verify your download

Every release also ships a `SCCoPilot.exe.sha256` file — the SHA-256 checksum of
that exact build. If you want to confirm your download wasn't corrupted or altered,
compare it. In PowerShell, from the folder you downloaded into:

```powershell
Get-FileHash SCCoPilot.exe -Algorithm SHA256
```

The hash it prints should match the contents of `SCCoPilot.exe.sha256` from the same
release. The app runs this same check for you automatically whenever it installs an
update.

## First run

On launch it finds your `Game.log` automatically, scanning your drives and every
Star Citizen channel (LIVE, PTU, EPTU, HOTFIX, TECH-PREVIEW). If it can't find
it, open Settings (the gear button, top right) and either browse to the file or
run auto-detect again. The top bar shows whether the log is connected.

Start it before or during a session and it picks up contracts as you accept them.

## Is it safe to use?

Yes. SC CoPilot does two things, both passive:

- It reads `Game.log`, a plain text file the game writes to your disk.
- If you turn on OCR (off by default), it captures your screen the same way OBS
  or ShadowPlay do.

It never reads or writes the game's memory and never attaches to the game
process, so there's nothing for anti-cheat to object to. It behaves like a
Discord or OBS overlay.

## What it does today

These are the hauling tools it ships with right now. More areas of the game are
planned.

### Contract tracking

Active contracts show as a list of cards, one per mission, with each leg's
pickup and drop-off, the cargo and SCU involved, and the reward. Completed
missions move to a history tab that keeps a running total of what you've earned.
Everything is read live from the log, so the cards update as you accept, deliver,
and complete contracts in game.

The moment you accept a contract, the card also fills in detail straight from the
game's own mission data: who's offering it, the expected payout up front (instead
of "reward pending" until you finish), the commodity, the route class, cargo grade,
difficulty, time limit, the reputation rank it's offered at and the reputation it
pays — plus an "illegal" flag on unlawful work. The bundled data is the starting
point; the live log and, if you use it, OCR confirm or correct it, and anything
that disagrees is flagged rather than quietly overwritten.

The log names delivery destinations but never names pickup stations, so a pickup
sometimes shows as raw coordinates. The card now labels it with the region from the
mission data when it can; you can also use the "name" button to give it a precise
label — saved against those coordinates and reused for any future contract at the
same spot.

For a contract that delivers to more than one place, each destination shows what
share of the contract's cargo drops there (60% / 30% / 10%) and the crates that
make up that portion, so you can see how it splits across stops at a glance.

The history tab shows profitability per contract — reward, SCU, duration, the
aUEC-per-SCU and the contract giver — with a text filter to find a run and an
Export to CSV button for your own bookkeeping.

### Route planning

The Route tab reorders the stops across your active contracts to cut down travel.
Pickups always come before their drop-offs, the running cargo load stays under
your ship's hold size, and distances use real orbit-to-orbit values so a short
quantum hop is preferred over a long one.

- Pick your ship from the dropdown to set the hold size, or use Detect to grab
  the ship you're currently flying. There's an optional manual SCU override.
- Stops show as colour-coded cards. Drag them to set your own order, or hit
  Recalculate to go back to the optimal route. Skip a stop you can't do (a pickup
  that bugged out, say) and it drops from the route and overlay; restore it from
  the Skipped strip. Mark a stop done by hand if the game didn't detect it, or
  push one marked collected back to to-collect if the game or a stray click ran
  ahead of you.
- Add your own stop with the "Add stop" button — somewhere to go that isn't a
  cargo contract (refuel, a bounty, pick up a personal box). Pick a location and
  it joins the route like any other stop, ordered and distance-measured, but it
  uses no cargo hold. Mark it visited or remove it any time.
- Each stop also shows the station's facilities — its landing-pad sizes and the
  services it has (refuel, repair, cargo, and more) — and, with online features
  on, the live Hydrogen (H2) and Quantum Fuel (QF) prices to refuel there, so you
  can see at a glance where to top up along the way.
- Below the cards, an orbital map draws your route over the current star system
  from a top-down view, using the real in-game body positions so it matches the
  Starmap. Scroll to zoom, drag to pan.

### Cargo loading

The Cargo tab is a top-down loading planner for your ship's bay. It takes the
cargo you'll carry on the active route and shows where to stack each load so the
first drop-off sits nearest the door — you unload last-in, first-out with no
shuffling.

Stacking is adaptive: when the hold has room, each destination's crates stay
together as one clean block; when space is tight, loads interlock to use every
cell — the cleaner layout never costs you capacity.

The route and the loading are solved together. When your cargo won't all fit in
the order your route happened to choose, the planner tries better pickup orders,
packs each one, and flies the one that stows the most — so the old "can't be
stowed" cases are almost entirely gone, and it never leaves more behind than the
routed order would. The search runs in the background, so a big route change
doesn't freeze the window. You can tune it under Settings > Engine: turn the
reordering on or off, or set how much extra cargo a reorder has to fit before
it's worth reshuffling your route. And if a load still genuinely can't be fully
stowed, a banner on the Cargo tab and the overlay tells you exactly how much to
leave behind for a later trip.

Popular haulers come with a hand-built bay layout; any other ship gets a grid
generated from its SCU capacity. You can group loads by destination, contract, or
commodity, pick which door you'll use, and the stacking re-optimises toward it.
The load-order list tells you what to load first to last and how each load breaks
down into crates. If a bundled layout is wrong, you can edit it — add or remove
bays and doors, set the dimensions, and save your own version per ship.

The bay's colours are yours to change too: a Colours sub-tab lets you restyle
the fills used for each grouping and the route-trace border, save schemes as
named profiles, and share them as JSON. Colour-blind presets are included, and
a reset always takes you back to the default.

### Trade and backhaul

The Trade tab and a backhaul panel on the Route tab use live market data to
suggest the most profitable cargo to carry — from a terminal you choose, or
automatically as a return load from your last drop-off so you don't fly home
empty. Each suggestion shows the buy and sell terminals, margin, ROI, distance,
and the profit for your ship's hold.

There's also a trade-run finder: point it at where you are now — or anywhere in
a system — and it searches out the most profitable buy-then-sell runs from
there, chaining multiple hops when that beats a single run, ranked by profit.

The online features — trade and backhaul, fuel prices, and the component prices
and sellers in the Search tab — are the only part of the app that uses the
internet, all behind one switch. Prices are fetched on demand and cached for about
half an hour, and the Trade tab tells you whether what you're looking at is a fresh
pull or cached prices. If you're offline it just shows a quiet note and everything
else keeps working. There's a single checkbox to turn online features off entirely.
No account or API key is needed.

### Search, ship spec and upgrades

The Search tab is a lookup over the whole game, plus a ship-outfitting planner. It
has three pages:

- **Search** — one box over locations (by name or service, ordered by distance,
  and optionally only ones your ship's pad fits) and ship components (browse the
  whole catalogue; pick one to see where it's sold and for how much, nearest
  first).
- **Ship spec** — pick any ship, or use the one you're flying to start from its
  stock loadout, then set the component fitted in each individual slot. Save your
  loadouts by name and load them back later.
- **Upgrade planner** — works from your saved spec: every slot shows its current
  part with an "upgrade to" picker, and the upgrades you choose become a
  buy-route — the nearest or cheapest seller for each, ordered by travel and drawn
  on a mini orbital map.

Browsing components and ship slots works offline; the prices, sellers and the
buy-route use the same optional online features as Trade. Ship loadout data comes
from the community Star Citizen Wiki and component data from UEX.

### Locations

A searchable catalogue of every station, city and outpost. Filter by landing-pad
size or the services you need — refuel, repair, cargo, and so on — and see the
in-game coordinates the app has learned for the places you've visited.

### Wallet

The Wallet tab keeps a running aUEC ledger. The game doesn't write your balance
to the log, so the balance is an estimate: set it once to anchor your current
in-game balance, and the ledger tracks forward from there — income from mission
rewards and commodity-kiosk sales, expenses from shop purchases, all read from the
log. If it drifts,
reconcile again to re-anchor. It shows session and lifetime totals and stays
entirely local. Re-reading the log on a later launch never double-counts.

### Refuelling

At the top of the ReFueling tab is the nearest station your current ship can land
and refuel at from where you are now — pad-aware, so it only suggests one with a
pad your ship fits, with the distance and, when online, the live H2/QF price
there. The same line also shows on the overlay, under the game-version chip. When
you plot a quantum route, the header also names your projected start location and
flags a long-haul jump that'll burn more quantum fuel than your recent hops.

If you run refuelling contracts, the ReFueling tab tracks them alongside your
hauls — the fuel types and rates involved and the reward. The reward isn't in the
log, so you can enter it by hand or let OCR read it off the contract screen (see
below) and have it credited to your wallet on completion.

### Overlay

A small in-game overlay shows your route as a hop itinerary: a prominent NEXT hop
with where to fly, the distance, and everything to pick up or drop there, the
following hops below it, and completed stops collapsed into a summary that
advances as you go. It's frameless and stays on top, and you can bind a hotkey to
toggle it (below).

You can also add the cargo bay alongside it — switch on Show cargo layout under
Settings > Overlay to mirror the current load as a read-only column, so you can see
how to stack while you load. It locks to the left, under the matching drop-number
badges, and grows down and to the right; the panel hugs the bay rather than leaving
empty space beside it; and a Cargo size slider sets how large it is by default (it
still scales up with the overlay window). Long pickup and destination names wrap to
the line below instead of stretching the panel out.

### Global hotkeys

Under Settings > Hotkeys you can bind a shortcut that works even while Star
Citizen has focus — toggle the overlay, mark the next hop done, skip the next stop,
or trigger an OCR wallet read, all without leaving the game. Click the field and press
your combo (a modifier plus a key, or a standalone key like F8). It uses a passive
keyboard hook, the same approach Discord and OBS use, so it needs no admin rights
and never takes the key away from the game.

### OCR (optional)

Some values are shown on screen but never written to the log — a refuel payout,
fuel rates, your wallet balance, a cargo contract's crate size and pickup names.
The OCR feature, under Settings > OCR and off by default, reads them by capturing
the screen.

You add a region, draw a box over the value on a frozen screenshot, and choose
when it reads (on a game event, a hotkey, or a timer) and where the value goes.
There's also an auto wallet update: pick a screen key to watch, and opening that
screen reads your balance and reconciles the ledger only if it changed. A balance
is only committed once it's read twice in agreement, so a momentary misread is
thrown away rather than written.

Run Star Citizen in borderless windowed mode for this (exclusive fullscreen
captures black), and box the number only, not its label.

The [OCR setup guide](docs/OCR.md) walks through all of it step by step —
turning it on, adding and positioning each kind of region, triggers, the auto
wallet update, and troubleshooting.

### Updates

The app checks for a newer release shortly after launch (there's a toggle to
turn that off) and lets you know if one's available. Settings > Update shows
your current version, the latest, when it last checked, and a button to update.
When you update, it downloads the new build, verifies it, and restarts itself
on the new version.

## Where your data lives

Everything is stored under `%APPDATA%\sc-copilot` — your settings, contract
history, wallet ledger, and learned location names. Nothing leaves your machine
apart from the optional market lookups and the on-launch update check.

## Reporting a bug or requesting a feature

Open an issue:

- [Report a bug](https://github.com/Maxybo/SC-CoPilot/issues/new?template=bug_report.yml)
- [Request a feature](https://github.com/Maxybo/SC-CoPilot/issues/new?template=feature_request.yml)

For bugs, please include your version (shown in the title bar and under
Settings > Update). If you can, attach the tail of the log at
`%APPDATA%\sc-copilot\sc-copilot.log` — it makes problems much easier to track
down.

## License

SC CoPilot is free to download and use for personal, non-commercial purposes.
Redistribution, modification, and reverse engineering are not permitted. See
[LICENSE](LICENSE) for the full terms.

SC CoPilot is an independent project and is not affiliated with, endorsed by, or
sponsored by Cloud Imperium Games or Roberts Space Industries.

