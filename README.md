# Min-Max Self-Coached

A single-file training app. Nippard's Min-Max Phase 2 skeleton, RP's autoregulation,
adapted to barbell anchors, a home gym, 4 days a week and a 35-45 minute cap.

`index.html` is the entire app — no build step, no dependencies, no server.

---

## Getting it on your phone

### Option A — GitHub Pages (recommended)

1. Create a repo (a public one is fine — there's nothing sensitive in here).
2. Upload `index.html` to the root.
3. **Settings → Pages → Source: `main` / root → Save.**
4. Wait a minute, then open `https://<your-username>.github.io/<repo>/` on your phone.
5. **iOS:** Share → *Add to Home Screen*. **Android:** ⋮ → *Add to Home screen*.

It then launches full-screen with no browser chrome and works offline.

### Option B — no GitHub

Email yourself `index.html`, open it, and add to home screen. Works the same, but
updating means re-sending the file.

---

## Your data

Everything lives in **localStorage on that one device**. Nothing is uploaded anywhere.

Two consequences worth taking seriously:

- Clearing site data / "clear browsing history" **wipes your log**.
- The phone and the laptop each keep a *separate* log — they do not sync.

So: **Settings → Download backup (JSON)** every couple of weeks. Restore from the same
screen. There's also a CSV export shaped to match the `Blank Log` tab of
`Min-Max_Adapted_Program.xlsx` if you want to keep long-term records in Sheets.

> If you ever start a session on the wrong device, finish it there and export — the
> coaching reads your logged history, so a split log gives you wrong weight targets.

---

## How to use it

Open it, answer the four readiness questions, hit start. It walks you one exercise at a
time and tells you the weight, the reps and the RIR for every set. Log the set, the rest
timer starts itself.

- **"Short on time"** on the home screen trims to Tier A only.
- **Swap this exercise** inside any exercise card if the equipment isn't free. The slot
  keeps its sets/reps/RIR schedule; only the movement changes.
- The week advances automatically after you finish a Pull day. Override it in Settings.

---

## Changing the program

All of it is the `PROGRAM` object near the top of the `<script>` block in `index.html`.
It's plain, commented data — edit it in any text editor.

```js
{id:"bench", n:"Barbell Bench Press", tier:"A", sets:2, rep:[4,6], rest:180, inc:5,
 rir:{e:[1,2], h:[0,0]}, tech:"myo", subs:[...], note:"..."}
```

| Field | Meaning |
|---|---|
| `tier` | `"A"` never cut, `"B"` dropped when short on time / under-recovered / deloading |
| `rep` | `[low, high]` — hit `high` at target RIR and the weight goes up |
| `rir.e` / `rir.h` | RIR per set on easy weeks (1, 7) and hard weeks (2-6, 8-12) |
| `inc` | lb added when you earn a jump |
| `tech` | `"myo"`, `"drop2"`, `"hold"` — applied to the last set in Block 2 only |
| `ss` | superset tag; exercises sharing a tag are paired |
| `pair` | marks this as the high-rep back-off set of another exercise |

Changing an exercise's `id` orphans its history, so keep ids stable if you want to keep
your load progression.

---

## What the coach actually does

**Load targets (per exercise, per set, session to session):**

| Last time | Next time |
|---|---|
| Hit the top of the range at target RIR | Add one increment; 2+ reps over adds more |
| Inside the range | Same weight, chase +1 rep |
| Inside the range but stopped short of target RIR | Same weight, go closer to failure |
| Below the range, once | Hold — one miss is noise |
| Below the range, twice in a row | Drop ~8% and rebuild |

**Readiness** (soreness / sleep / energy / joints, plus whether you missed targets in your
last two sessions of this day):

- **Green** — run as programmed.
- **Yellow** — Tier B dropped, 3-set exercises drop to 2, +1 RIR on the heavy compounds.
- **Red** — one set per main lift at 85% load and +3 RIR. A session you bank, not one you win.

Repeated Red days surface a "take your deload now" banner rather than waiting for week 7.

**Deload and Red-day loads are logged but never used as the baseline for your next
target** — otherwise every deload would permanently ratchet your working weights down.
