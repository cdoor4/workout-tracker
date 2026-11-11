# Session config schema

Each session is one JSON block inside `index.html` (`<script type="application/json" id="session">`). The CSS/JS never change per session; only this block does. That is what lets an AI coach generate a session from training history and read the results back.


```json
{
  "id": "s35",                       // storage key — unique per session
  "session": "S35", "type": "Push", "date": "YYYY-MM-DD",
  "eyebrow": "Block 2 · Week 2 · S35", "title": "Push Day",
  "meta": ["Mon 2 Nov 2026", "Home gym", "..."],
  "budget": [[10, "min warm-up"], [38, "min main work"], [7, "min cooldown"]],
  "banners": [{"level": "stop|warn", "title": "...", "text": "..."}],   // active constraints
  "warmup":   {"label": "Warm-up",  "time": "10 min", "items": [["key", "label", "dose"], ...]},
  "main":     {"label": "Main work","time": "38 min", "exercises": [ EXERCISE, ... ]},
  "cooldown": {"label": "Cooldown", "time": "7 min",  "items": [["key", "label", "dose"], ...]},
  "checks": [{"key": "back", "label": "Back during the session (0 = nothing)"}],  // 0-10 chips, one per active flag
  "quote": {"text": "...", "by": "Author, Source"},      // B: one quote matched to this session's situation (header)
  "lines": [{"text": "...", "by": "..."}],                 // 3-5 session-matched quotes, rotated under the rest timer
  "whys": ["..."],                                         // D: the lifter's own reasons — every 3rd rest + shown on Finish
  "footer": "..."
}
```

EXERCISE:
```json
{
  "key": "bench", "tag": "A", "name": "Bench press", "rx": "57.5kg · 3 × 8 · RPE 7 · rest 2 min",
  "why": "last result / reason", "cues": ["..."], "rule": "<b>Up:</b> ... <b>Down:</b> ...",   // rule may use <b>
  "ramp": [["20kg", 10], ["35kg", 8], ["45kg", 5], ["52.5kg", 2]],   // optional: ticked warm-up sets (≥4 on squat/hinge)
  "rampRest": 45, "rampLastRest": 90,                  // optional: rest timer after each ramp tick (defaults 45s / 90s after the last ramp set)
  "sets": 3, "reps": 8,
  "load": 57.5, "loads": [52.5, 55, 57.5, 60, 62.5],   // optional: DB weights list, or use "loadStep": 2.5 for barbell
  "loadUnit": "kg", "unit": "reps", "repStep": 1,      // unit "s" + repStep 5 for timed holds/carries
  "rest": 90,                                            // optional: rest timer seconds after ticking a set (default 60)
  "noRpe": true,                                         // optional: band/isolation work
  "setLabel": "Round",                                   // optional: circuits
  "superset": {"key": "pallof", "label": "Pallof", "dose": "10/side", "text": "Between sets: ..."},  // ticked after each set
  "tickOnly": true, "dose": "45s/side"                   // optional: stretch/item with a single tick (no sets)
}
```
Warm-up/cooldown items take an optional 4th element = rest seconds (e.g. `["fp1", "Face pulls 1", "12–15", 30]`) to start the timer on tick — omit it for circuits/paired items. Every exercise card gets a notes box automatically (`exNotes.<key>`). Ticking a ramp set or a main-work set starts a rest timer (default 60s, ±15s buttons, beeps/vibrates at zero, then counts up); the timer is local only and not saved.

## Stored document (what the coach reads back)

```
{ session, type, date, startedAt, finishedAt (ISO, UTC), updatedAt,
  ticks: { <warmup/cooldown key>: true, <key>-w<n>: true (ramp sets), <superset key><n>: true, <tickOnly key>: true },
  lifts: { <key>: [ {done, load, reps, rpe}, ... ] },
  exNotes: { <key>: "text" }, checks: { <key>: 0-10 }, notes: "text" }
```
Reading rules: a set with `done: false` was NOT performed (its reps are just the prefilled target). `rpe: null` = not logged. Times are UTC.

