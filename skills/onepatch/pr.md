# PR delivery

Read this when delivering as a pull request. The branch carries the fix through CI to green; the PR is opened early as a draft and marked ready only at the end.

## Worktree and branch

Do all PR work in its own worktree, from a clean state of the delivery base. For CI input the base is the failing commit's `head-sha`; otherwise it is the current checkout's tip.

- CI input: branch `repair/<job-id>` (digits only for the id segment).
- Other input: branch `patch/<slug>`, a short slug of the fix.

```sh
git fetch origin
git worktree add -b "<branch>" "<project-root>/wt/<branch>" <base-sha>
```

If the main checkout has unrelated dirty work, leave it alone — the worktree is how the turn stays isolated. If a PR for the branch already exists, push to it and reuse its URL.

## Merge the base, push, open a draft PR

Merge the default branch into the turn branch first and resolve conflicts if straightforward. If resolution needs design choices, stop and ask the author. Do not push until this merge is done.

```sh
git fetch origin <default-branch>
git merge origin/<default-branch>
git status
git push -u origin "<branch>"
```

Open the PR immediately, as a **draft**:

```sh
gh pr create --draft \
  --base "<pr-base>" \
  --head "<branch>" \
  --title "<short cause>" \
  --body "$(cat <<'EOF'
## Summary
- <one or two sentences: what broke and what this changes>.
- Origin: <the author's change | inherited from base>.
- Root cause: <one or two sentences>.
- Coverage impact: <none | reduced because ...>.

## Test plan
- [ ] Failure logs inspected
- [ ] Local reproduction / equivalent check
- [ ] Merged base into the turn branch
- [ ] Every blocking check green on the PR
EOF
)"
```

The draft PR is the verification vehicle, for two reasons:

- A branch push may trigger nothing when the repo restricts push builds to the default branch. The full check matrix exists only on `pull_request`.
- `pull_request` CI checks out the merge with base **continuously** as the base moves, where a local `git merge` is a snapshot that goes stale immediately.

## Drive every blocking check green

```sh
gh pr checks <pr-url> --watch
```

Green means **every blocking check on the PR** is successful — not only the workflow that originally failed. A turn can leave its own job green while a different workflow stays red; that branch is still unmergeable.

- **All blocking checks green**: mark ready.
- **Failure**: pull the failed logs, return to the fix on the same branch. Commit additional fixes and push; the PR re-runs itself. Re-merge the base only when local verification needs a current base — the merge ref updates on its own.
- **Cancelled / timed out**: report status and ask the author whether to retry or stop.

Do not busy-loop with short sleeps; use `--watch` or a real completion wait.

## Mark ready and report

```sh
gh pr ready <pr-url>
```

Then report in the conversation:

1. **Root cause** (brief)
2. **Origin** — the author's change or inherited from base, and the resulting base
3. **What changed** (files / behavior), including `Coverage impact` if not none
4. **Branch** and **PR URL**
5. **Green run URL** for the blocking checks
6. Anything left for the author — review notes, follow-ups, residual risk, and anything this turn deliberately did not address
