# SC CoPilot

SC CoPilot is a desktop companion for Star Citizen hauling. It reads the game's
log while you play and keeps track of your cargo contracts — what to pick up,
where to drop it, how much you're owed, and where you've been. On top of that it
plans an efficient route between your stops, lays out your ship's cargo bay,
keeps a running balance, and can show a small overlay on top of the game.

It runs entirely on your machine. No account, no launcher, nothing to configure
to get going.

## Download

Get the latest `SCCoPilot.exe` from the [Releases page](https://github.com/Maxybo/SC-CoPilot/releases/latest).
It's a single file — no installer, no dependencies.

The exe isn't code-signed, so Windows SmartScreen may warn you the first time you
run it. Click "More info", then "Run anyway". That warning is normal for small
independent apps (signing certificates are expensive).

The app keeps itself up to date: when a new release is out it offers to download
and install it, and you can check manually under Settings > Update.

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

## What it does

### Contract tracking

Active contracts show as a list of cards, one per mission, with each leg's
pickup and drop-off, the cargo and SCU involved, and the reward. Completed
missions move to a history tab that keeps a running total of what you've earned.
Everything is read live from the log, so the cards update as you accept, deliver,
and complete contracts in game.

The log names delivery destinations but never names pickup stations, so a pickup
sometimes shows as raw coordinates. Use the "name" button on the card to give it
a label — it's saved against those coordinates and reused for any future contract
at the same spot.

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
  the Skipped strip. Mark a stop done by hand if the game didn't detect it.
- Below the cards, an orbital map draws your route over the current star system
  from a top-down view, using the real in-game body positions so it matches the
  Starmap. Scroll to zoom, drag to pan.

### Cargo loading

The Cargo tab is a top-down loading planner for your ship's bay. It takes the
cargo you'll carry on the active route and shows where to stack each load so the
first drop-off sits nearest the door — you unload last-in, first-out with no
shuffling.

Popular haulers come with a hand-built bay layout; any other ship gets a grid
generated from its SCU capacity. You can group loads by destination, contract, or
commodity, pick which door you'll use, and the stacking re-optimises toward it.
The load-order list tells you what to load first to last and how each load breaks
down into crates. If a bundled layout is wrong, you can edit it — add or remove
bays and doors, set the dimensions, and save your own version per ship.

### Trade and backhaul

The Trade tab and a backhaul panel on the Route tab use live market data to
suggest the most profitable cargo to carry — from a terminal you choose, or
automatically as a return load from your last drop-off so you don't fly home
empty. Each suggestion shows the buy and sell terminals, margin, ROI, distance,
and the profit for your ship's hold.

This is the only part of the app that uses the internet. Prices are fetched on
demand and cached for about half an hour; if you're offline it just shows a quiet
note and everything else keeps working. There's a single checkbox to turn online
features off entirely. No account or API key is needed.

### Wallet

The Wallet tab keeps a running aUEC ledger. The game doesn't write your balance
to the log, so the balance is an estimate: set it once to anchor your current
in-game balance, and the ledger tracks forward from there — income from mission
rewards and expenses from shop purchases, both read from the log. If it drifts,
reconcile again to re-anchor. It shows session and lifetime totals and stays
entirely local. Re-reading the log on a later launch never double-counts.

### Refuelling

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

### Global hotkeys

Under Settings > Hotkeys you can bind a shortcut that works even while Star
Citizen has focus — toggling the overlay, for example. Click the field and press
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

### Updates

The app checks for a newer release shortly after launch, at most once a day, and
lets you know if one's available. Settings > Update shows your current version,
the latest, when it last checked, and a button to update. When you update, it
downloads the new build, verifies it, and restarts itself on the new version.

## Where your data lives

Everything is stored under `%APPDATA%\sc-copilot` — your settings, contract
history, wallet ledger, and learned location names. Nothing leaves your machine
apart from the optional market lookups and the once-a-day update check.

## Reporting a bug or requesting a feature

Open an issue:

- [Report a bug](https://github.com/Maxybo/SC-CoPilot/issues/new?template=bug_report.yml)
- [Request a feature](https://github.com/Maxybo/SC-CoPilot/issues/new?template=feature_request.yml)

For bugs, please include your version (shown in the title bar and under
Settings > Update). If you can, attach the tail of the log at
`%APPDATA%\sc-copilot\sc-copilot.log` — it makes problems much easier to track
down.
