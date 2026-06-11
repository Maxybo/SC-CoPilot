# OCR setup guide

SC CoPilot reads almost everything it needs from `Game.log`, but a few values
only ever appear on screen: a refuel contract's payout, fuel types and rates,
your wallet balance, a cargo contract's crate size and its pickup names. The
OCR feature reads those off the screen for you — you draw a box over where a
value appears, and the app captures that patch of screen and turns it into a
number (or text) at the moment you choose.

It's off by default, and it's passive: it captures the screen the same way OBS
or ShadowPlay do, never touches the game's memory or process, and everything is
read by Windows' own built-in OCR engine, locally. Captures are processed in
memory and discarded — nothing is saved or uploaded.

This guide covers the whole feature: [turning it on](#turning-it-on), [adding
and positioning regions](#adding-a-region), [triggers](#choosing-when-it-reads),
[where values go](#choosing-where-the-value-goes), [the auto wallet
update](#auto-wallet-update), and [troubleshooting](#troubleshooting).

## Before you start

- **Run Star Citizen in borderless windowed mode** (Options > Graphics > Window
  Mode). Exclusive fullscreen can't be captured — you'd get black frames.
- Nothing to install: the app uses the OCR engine built into Windows 10 and 11.
  Leave **Engine** on "Auto (recommended)". The line under it shows what's
  available on your machine.
- If you play across several monitors, note which one the game is on — each
  region is tied to a monitor.

## Turning it on

Open Settings (the gear button, top right) and go to the **OCR** page. Tick
**Enable OCR**. Nothing happens yet — OCR only ever reads the regions you
define, when their triggers fire.

The **Capture delay** next to it is a global "wait before reading" applied to
every read, so the screen has time to finish drawing — a contract's details
appear a beat after you accept it, for example. The default (1500 ms) is a good
starting point.

## Adding a region

A region is one value you want read: where it is on screen, what kind of value
it is, when to read it, and where the result goes. Click **Add** and pick a
preset — each comes pre-wired with the right value type, trigger and
destination, so for most of them the only thing left to do is position the box:

- **Refuel reward** — the payout on a refuel contract. Reads when you accept a
  refuel contract and fills that contract's reward in the ReFueling tab (and
  your wallet is credited when you complete it, since the game never logs it).
- **Refuel mission details** — the contract description that quotes the rates
  ("...at the rate of 240 aUEC per SCU of Hydrogen..."). Reads on accept and
  fills the fuel types and per-SCU rates on the ReFueling tab.
- **Cargo mission details** — a cargo contract's details and objectives. Reads
  on accept and fills the contract's max crate size (used by the Cargo tab's
  loading plan) and names coords-only pickups, validated against known
  stations. Box the contract's text area generously — it copes with the
  two-column layout.
- **Wallet balance** — the balance on the mobiGlas user screen (F1/F2). No
  trigger by default: it's the region the [auto wallet
  update](#auto-wallet-update) uses, or bind it to a hotkey.
- **Fuel quantity** and **Max crate size** — read-and-show values with no
  default trigger; the result is shown and logged but changes nothing.
- **Custom…** — start from scratch and wire it yourself.

Each region's editor lets you change any of this: its label, what it **Reads**
(currency, SCU, whole number, fuel quantity, or free text — this drives the
parsing), the **Monitor** it lives on, and **Then** — where the value goes.

## Positioning the box

This is the part worth doing carefully.

1. In game, bring the value up on screen (open the contract, the mobiGlas,
   whatever shows it).
2. Switch to SC CoPilot and click **Define region…**. The app hides itself,
   freezes a screenshot of that monitor, and lets you drag a rectangle on the
   still image. Draw a box around the value and release; Esc cancels.

Two rules make reads dramatically more reliable:

- **Box the number only — not its label.** Keep "aUEC", currency symbols,
  icons and neighbouring text out of the box. A symbol clipped at the edge can
  swallow the first digit of the number.
- **Don't crop the digits.** Leave a little breathing room around the value so
  nothing is cut off. Tight, but not touching.

The box is stored as a fraction of the monitor, so a resolution change is fine
— but if you move the game to another monitor, change its window size, or
change the game's UI scaling so the value sits somewhere else, redefine the
region.

## Testing a region

**Test read** captures the region right now and shows what it read, e.g.
`Read: "19,750" → 19750`. One thing to know: it reads the screen exactly as it
is at that moment — including SC CoPilot's own windows if one is covering that
spot. So either keep the game visible on that monitor while you test, or do the
real test in game: bind the region to a hotkey, press it while playing, and
check the result. The triggers always read the game, because you're in it when
they fire.

## Choosing when it reads

Each region can have any mix of three trigger kinds:

- **Events** — read automatically at a game moment the app detects from the
  log: a refuel or cargo contract being accepted or completed, or any contract
  accepted/completed. This is the set-and-forget option and what the contract
  presets use. The capture delay is applied first, and duplicate firings a
  moment apart are collapsed into one read.
- **Hotkey** — a global shortcut that works while the game has focus (click
  the field and press your combo, e.g. Ctrl+Alt+R; Clear unbinds it). The
  game still receives the key — the hook is passive, the same approach the
  app's other global hotkeys use.
- **Poll every N seconds** — re-read on a timer, for a value you want tracked
  continuously. 0 means off.

## Choosing where the value goes

The **Then** dropdown decides what happens with a successful read:

- **Show & log only** — display it and write it to the app's log; change
  nothing. Good for values you just want captured.
- **Fill refuel job reward** — set the reward on the refuel contract it was
  read for (the contract being accepted, for event reads; your latest refuel
  job, for hotkey reads). Credited to the wallet when the contract completes.
- **Fill refuel fuel types + rates** — parse the quoted rates into the
  ReFueling tab's fuel column.
- **Fill cargo crate size + locations** — set the contract's max crate size
  and name its unnamed pickups.
- **Reconcile wallet balance** — anchor the Wallet ledger to the read balance,
  recording an adjustment only if it differs from the running total.

## Auto wallet update

The hands-off way to keep the Wallet tab honest. Once set up, simply opening
your mobiGlas updates the ledger.

1. Add a **Wallet balance** region and define its box over the balance shown on
   the user screen (press F1 or F2 in game to bring it up, then box the number
   only — keep the aUEC symbol out).
2. Back on the OCR page, tick **Update wallet balance when I open the user
   screen** in the Auto wallet update group.
3. Pick the **Watch keys**. F1 and F2 both open screens that show the balance;
   if one of them reads poorly for you, untick it and keep the reliable one.
4. Leave the **User-screen delay** at 2000 ms to start — it's how long the app
   waits after the keypress for the screen to open and draw, separate from the
   global capture delay.

When you press a watched key, the app waits the delay, reads the region, and —
because a wallet write has to be right — holds itself to a higher standard than
a normal read: if the frame was empty or garbled it quietly re-samples for a
few seconds, and it only touches the ledger once the **same value has been read
twice**. A one-off misread is thrown away, and if it can't confirm a value it
leaves the ledger alone and tells you, so you can just press the key again.
If the confirmed balance matches the running total it does nothing at all.

## Troubleshooting

- **Reads come back black or empty in game** — the game is in exclusive
  fullscreen. Switch to borderless windowed.
- **Contract reads are empty or cut off** — the screen hadn't finished drawing.
  Raise the global **Capture delay** (try 2000–2500 ms).
- **The wallet read "couldn't confirm"** — raise the **User-screen delay**
  (3000 ms suits slower scenes), and if one watch key is consistently worse,
  untick it. The balance fades in on the user screen, so a longer delay gives
  it time to be fully drawn.
- **It reads the wrong digits, or drops the first one** — re-draw the box
  tighter around the digits only, keeping the currency symbol and any
  neighbouring text or icons outside it.
- **Small or low-contrast text won't read** — tick **Pre-process (upscale +
  contrast)** on that region. Failed reads are also automatically retried with
  a few image treatments, so an occasional miss recovers on its own.
- **"No OCR engine available"** — Windows OCR needs a language pack with OCR
  support; the English packs that ship with Windows 10/11 include it. Check
  Settings > Time & Language > Language & region.
- **Test read shows the app's own text** — expected; it reads whatever is on
  that patch of screen right now. Test in game with a hotkey instead (see
  [Testing a region](#testing-a-region)).

Every read is also written to the app's log at
`%APPDATA%\sc-copilot\sc-copilot.log` — the raw text it saw and the value it
parsed — which makes it easy to see what's happening when a region misbehaves.

---

Back to the [main README](../README.md).
