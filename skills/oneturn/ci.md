# CI input

Read this when the input is a failed GitHub Actions job. Recover it with a tight local fix loop. Prefer a minimal fix that reproduces the failure locally. Stop for author approval before any serious or architectural change.

Every step below is part of the procedure. Skip a step only when this skill marks it optional, or when the author overrides it in the conversation.

## Resolve the job

The input is a job id (preferred; used in the branch name), a run id, or a GitHub Actions URL for a run or job. If none was given, ask for one before proceeding. Do not invent a job id.

Resolve ids with `gh`:

```sh
# From a run URL or run id — list jobs and pick the failed one
gh run view <run-id> --json databaseId,headBranch,headSha,url,jobs

# Job details / logs
gh run view --job <job-id> --log-failed
```

Record for the rest of the turn:

| Field | Source |
|---|---|
| `job-id` | Failed job database id (branch name uses this) |
| `run-id` | Parent workflow run id |
| `workflow-file` | Workflow file for the failed run |
| `head-branch` | Branch the failing run was on |
| `head-sha` | Commit SHA of the failing run |
| `default-branch` | Repo default branch (usually `main`) |
| failure summary | Failed step names + error tail from logs |

If several jobs failed in one run, repair them together only when they share a root cause; otherwise take the one the author named.

## Triage — where does this failure belong?

Do this **before** writing any fix. `head-branch` is where the failure was *observed*; it is not necessarily where the fix *belongs*.

```sh
# Does the same workflow already fail on the default branch?
gh run list --workflow <workflow-file> --branch <default-branch> --limit 5 \
  --json conclusion,headSha,databaseId,createdAt

# Which commit first went red — compare the last green SHA with the first red one
git log --oneline <last-green-sha>..<first-red-sha>
```

| Failure originates from | PR base | Notes |
|---|---|---|
| The head branch's own change | `head-branch` | If it is a **bot branch** (`dependabot/*`, `renovate/*`), do not base a PR on it: those get force-pushed on rebase and deleted on close, which orphans the PR. Either commit onto that branch directly — Dependabot stops rebasing a branch once a human pushes to it — or supersede it: close the bot PR and open one carrying both the bump and the fix. |
| The base / default branch (it reproduces there) | `default-branch` | The head branch goes green on its own once it picks up the fix. Fixing it only on the head branch leaves the default branch broken. |

Record the answer as `pr-base`. It is load-bearing: pull-request CI builds a merge of head into **base**, so a wrong base means CI faithfully verifies the wrong merge.

If one run holds both an inherited failure and a head-branch failure, say so and repair them as separate branches with different bases.

## Constraints

- Do **not** push to `main` / protected default branches.
- Do **not** force-push unless the author explicitly asks.
- Do **not** merge the PR.
- Verify with the project's own test and check commands (see its developer docs) at each step, not only at the end.
- One logical fix per commit when possible; keep the PR focused on the CI failure.

## Repair or mask?

Before moving on, state which one the fix is. A change that makes CI pass by removing the condition rather than fixing it — stubbed credentials, a skipped test, a relaxed assertion, a disabled check — is legitimate as a stopgap but **must be declared** in the PR body's `Coverage impact` line and to the author.

## Stops

Stop and report instead of iterating when any of these holds:

- **3** failed repair attempts on the same job;
- an attempt reveals a **different root cause** than the original theory — that is a re-triage signal, not a next attempt;
- the failure is flaky infrastructure (runner, network, third-party) with no code change needed: say so, re-run once after merging the base, and only open a PR if a real product or test fix was required.
