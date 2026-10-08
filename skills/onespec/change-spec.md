## Change spec record

A change spec is arranged in this order, and a new section goes where it belongs, not at the end:

1. Goal and design — what the change is and how it works.
2. Facts — what was verified, with dates.
3. `Builds` — one record per build, in order: executed builds, then the one planned build.
4. `Backlog` — always last.

An implementation line (for example, `--- Implemented / Next ---`) splits current state from planned deltas:

1. Text above the line is historical and may be inconsistent with later changes. This doesn't need fixing: the later version, and ultimately, code, wins.
2. Below the line, milestones are delta-only; they do not restate full behavior.

When reviewing a change spec:

1. Keep one canonical statement per behavior/default; later sections should reference it.
2. If duplicated rules disagree, flag drift; for current-state checks, code wins.

### Builds and numbers

The spec grows one build at a time. A build is numbered `<TAG><n>` when it is planned, never earlier, so only executed builds and the one planned build carry numbers. The backlog is a flat, unordered list of one-line items. Backlog items carry no build numbers. They have no acceptance checks, patches or ordering; anything beyond a one-line item is planned only when it becomes the next build. Outside a planning turn, add open questions and backlog items, never draft future builds.

Each build record begins with a `Status:` line:

- `Status: planned, awaiting author checkpoint`
- `Status: planned, approved <date>` — the author approved the plan at the checkpoint, by saying so or by invoking `onebuild` on it; the first patch lands it
- `Status: executed, pending author verification` — every patch is committed, but acceptance has a clause only the author can discharge
- `Status: implemented <date> (commits <TAG><n>.*)`

A patch is named `<TAG><n>.<k>`, the same string as its commit subject prefix. Patch numbers restart at 1 for each build.

A finished change spec is a record of what was built, not a plan: plan from this layout, never from a finished spec. Retiring a change spec keeps its `Status:` lines.
