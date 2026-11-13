# Crown Dial

A keyboard-only problem-solving timer for JEE practice. It enforces the method, so you don't have to rely on willpower.

## Open it

Double-click `crown-dial.html`, or open `file:///…/crown-dial.html` in Chrome or Firefox. It has no server, no build step and no dependencies.
On the first **Start** it asks for notification permission and unlocks audio. After a reload, sounds start again on your first keypress (browser rule).

## Modes (home screen keys)

| Key | Mode | What it does |
|---|---|---|
| `1` | Practice set | 10–15 IDs, one per line (or just a count → Q1..Qn). Block 90→75+15, 60→50+10, 45→37+8, custom = round(5/6) solve + rest log. Target pace defaults to 4.5 min. |
| `2` | Re-attempt | Paste any number of IDs (1–50). Single pass with a 10:00 cap each, then the marking screen. Marking has **no countdown** (my call). There is no 2:00 direction check and no target pace in this mode. |
| `3` | Blurt | 30:00 write, then 5:00 check. A loud alert sounds between them, and the overrun after 5:00 shows in red until `F`. |
| `4` | Mock paper | Paper 1 (3 h) → 45-min break → Paper 2 (3 h). `1/2/3` switch the active section P/C/M (each paper starts on P). |
| `5/6/7` | Break 5/10/15 | Counts down. After zero it counts up in red as overrun until `N`, and the overrun is recorded. |

## Keys

The bar at the bottom always lists only the keys that work right now.

| Phase | Keys |
|---|---|
| Setup | `Tab` next field · `Ctrl+Enter` start (or `Enter` in a single-line field) · `Esc` leave field, `Esc` again = back |
| Solving a question | `Enter` answer field → type → `Enter` commit (`Esc` cancels) · `B` then `B` = BLANK · `H` peek/hint · `P` park ↩ (pass 1 only) · `C` continue (after the 2:00 "Direction?") · `Space` pause · `Q` abandon (then `Enter` to confirm) |
| All resolved | `L` start the log |
| Log | `↑/↓` or `K/J` move · `Y` ✓ · `N` ✗ · `C/A/S/T` tag (sets ✗) · `Enter` "trigger → move" note · `F` finish · `Space` pause · `Q` abandon |
| Summary | `↑/↓` `K/J` · `Y` ✓ · `C/A/S/T` tag (sets ✗) · `Enter` note · `X` copy text · `N` new block |
| Blurt | `F` start check early (`F` again to confirm) · `F` finish (in check) · `Space` · `Q` |
| Mock | `1/2/3` section · `F F` end paper early · `Enter` start Paper 2 (in the break) · `Space` · `Q` |
| Break | `N` end break → home · `Space` |

In the summary, `N` means *new block*. To mark something ✗ there, press its tag letter.
Single-letter keys are ignored while a text field has focus.

## Rules it enforces

- A question ends only by **COMMIT** (a non-empty short answer) or **BLANK**. There is no skip key, and nothing ever auto-advances.
- At 2:00 on a pass-1 question you get a sound, a "Direction?" notification and a banner that stays until `C` or `P`.
- Target pace counts total time on the question:
  - Past target: a soft chime and amber.
  - At 2× target: the clock turns red and nags every 30 s.
- Parked questions get a second pass after pass 1, in order. Each gets a 5:00 cap (counted on its pass-2 time), and at the cap you get a loud alert and red, and must commit or blank.
- Peek (`H`) is recorded. That question is locked to ✗, and ✓ is refused with a reason.
- When solve time ends:
  - A loud alert sounds and the log countdown starts at that instant.
  - Unreached and still-parked questions become ✗ T.
  - An open question must still be committed or blanked. The time you spend on it is the solve overrun, and it eats into the log time.
- In the log, every question needs a mark and every ✗ needs a tag before `F` is accepted. Time past zero is the log overrun (red).
- Leaving the log early with `L` is allowed only once everything is resolved. Unused solve time is not added to the log.
- Pauses happen only with `Space`. Each one is logged with its duration, every clock excludes paused time, and no alerts fire while paused. **Closing the tab is not a pause.** The clock keeps running, and on reopen every alert that fell due fires at its true time.
- Times are never editable. Marks, tags and notes stay editable in the summary.
- Red is used only for overrun: 2× target, past a cap, and solve/log/break/check overrun.

## Summary (copy with `X`)

The summary lists:
- ✓ / attempted (committed) / set size
- the C/A/S/T tally
- how many questions were parked
- questions over target (count and IDs)
- the longest question
- the ✗ list (ID, tag, trigger → move)
- unattempted IDs (blank, unreached, or still parked at timeout)
- pauses (count and total)
- solve and log overrun
- every ✗ ID due for re-attempt at **+2 and +7 days**, with dates calculated in your local timezone

If the clipboard API is blocked on `file://`, a selectable text box appears instead.

## Self-test

Open `crown-dial.html?test`. It runs the built-in tests against the pure `Core` with synthetic timestamps. The page title becomes `PASS` or `FAIL n`, and your saved session is not touched.
The `core` and `selftest` script blocks also run unmodified in Node.

## Where data lives

All data is in `localStorage` under the key **`crownDial.v1`**:
- It holds the current block plus the last 30 finished or abandoned blocks (shown under "Recent" on home).
- It is saved after every action that changes state.
- Unreadable saved data is moved to `crownDial.v1.backup`, and the app starts fresh.
- Data is per browser and per file path. Clearing site data erases it.
