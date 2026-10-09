# OneSpec positioning

What OneSpec offers over other spec-driven development (SDD) approaches, and
the claims the user guide and website can make. Each claim names the
mechanism that backs it; a claim without one is listed under "Not yet
claimable".

Sources, read 2026-10-08:

- Birgitta Böckeler, [Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html), martinfowler.com
- GitHub, [Spec-driven development with AI: get started with a new open source toolkit](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- IBM, [What is spec-driven development?](https://www.ibm.com/think/topics/spec-driven-development)
- DeepLearning.AI, [Spec-Driven Development with Coding Agents: Why spec-driven development?](https://learn.deeplearning.ai/courses/spec-driven-development-with-coding-agents/lesson/9i8xmt/why-spec%E2%88%92driven-development%3F)

## One line

OneSpec is a lightweight spec-driven development toolkit for a developer
working with coding agents. It keeps the product's specs current: each change
is planned one small increment at a time, delivered with recorded evidence
that it works, and folded back into the specs when done.

## Where OneSpec sits

Böckeler distinguishes three levels of SDD:

- **Spec-first**: a spec is written for a task, guides the work, and is then
  discarded.
- **Spec-anchored**: the spec is kept after the task and used to evolve and
  maintain the feature.
- **Spec-as-source**: humans edit only the spec; code is generated from it.

All three tools she reviewed (Kiro, spec-kit, Tessl) work spec-first; only
Tessl aims beyond it. IBM recommends spec-anchored as the middle ground and
warns that spec-as-source needs a confidence in code generation that
non-deterministic models do not yet earn.

OneSpec is spec-anchored by construction. Durable specs describe the product
as currently intended; change specs carry one change while it is built, and
their durable content folds back into the durable specs. OneSpec does not
generate code from specs, so it avoids what Böckeler calls the downside of
spec-as-source: "Inflexibility *and* non-determinism".

OneSpec is positioned first for one developer working with coding agents: the
author who plans, approves and verifies. Which team shapes it scales to is
left to be seen in use.

## Advantages

Each advantage answers a problem the sources raise.

### Specs stay current

**Problem.** Spec-first specs are thrown away after the task, and spec-kit
ties each spec to a branch for one change request, not to the feature's
lifetime (Böckeler). Nothing keeps the spec true once the code moves on.

**OneSpec.** Two kinds of spec with one lifecycle: a change spec exists only
while its change is built, then `onewrap` reviews everything the change
touched, folds its durable rules into the parent spec as a diff the author
confirms, and retires it. Implementation detail (file paths, step order,
build records) is deliberately not folded, so the durable spec states
behavior only.

### Plan one increment, not the whole feature

**Problem.** Böckeler notes that small, iterative steps have historically
given the best control over software, and that SDD tools favor "lots of
up-front spec design". GitHub's flow specifies, plans and breaks down the
whole feature before implementing; IBM warns that over-specification wastes
effort on details that turn out irrelevant.

**OneSpec.** Only the next build is planned in detail; everything after it
stays a one-line backlog item. `oneplan` reviews the build just finished
before planning the next, so each plan starts from what was learned.

### Ceremony that fits the problem

**Problem.** Kiro turned a small bug fix into "4 user stories with 16
acceptance criteria"; spec-kit produced eight files for one feature.
Böckeler asks what problem size SDD is meant for.

**OneSpec.** Three sizes of work, each with its own path:

- an unplanned fix or small change: `oneturn` diagnoses, confirms with the
  author only when the remedy is not obvious, and delivers one verified
  patch, with no change spec;
- a planned change: one change spec file and the build loop;
- the whole product: the spec graph and coverage review, adopted when wanted.

`oneplan`, `onebuild` and `oneturn` work without any spec graph, so a team
can adopt the loop first and the graph later.

### Less to review

**Problem.** "I'd rather review code than all these markdown files": spec-kit
output was verbose and repetitive, and an SDD tool needs "a very good spec
review experience" (Böckeler).

**OneSpec.** One Markdown file per change, in ordinary prose, in a fixed
order: goal and design, verified facts, one record per build, a backlog of
one-liners. The Simplicity principle admits an artifact only when it improves
clarity, traceability, implementation, verification or change management. The
author reviews one build plan per checkpoint, then one commit per patch.

### Evidence, not checklists

**Problem.** Agents ignore specs or "go way overboard" despite elaborate
workflows (Böckeler); "AI is only as good as the instructions it receives"
(IBM). Spec-kit's definition of done is a checklist (Böckeler).

**OneSpec.** Every build declares an acceptance check before it runs, and the
scriptable parts are written into the project's tests at planning time.
`onebuild` requires each patch's test to fail before and pass after with the
whole suite green, and checks that every changed line traces to the patch's
stated purpose. Acceptance runs once over the final tree and is recorded in
the change spec with its provenance. A claim about the outside world (a
deploy, a published package, a provider calling back) passes only when the
real effect is observed; a step that cannot be observed blocks the build
instead of being marked done.

### The author stays in control

**Problem.** SDD tools assume one developer does product analysis, design and
coding (Böckeler); GitHub and DeepLearning.AI cast the human as the
architect who steers.

**OneSpec.** Every build stops at an author checkpoint before it runs, and
nothing is built until the author approves. Checks only the author can make
(a write to their own account, a paid call, a physical observation) are
listed as author actions, and the build stays open until they are reported.

### Traceable both ways

**Problem.** Specs drift from code and nobody can see where (IBM's "context
drift"; DeepLearning.AI's "context decay").

**OneSpec.** Code and tests name the spec section they implement or verify
with a `(spec)` reference. `onespec` and `onereview` report every spec section
as covered, partly covered or a gap, and also report behavior in the code
that no spec describes.

### No tool lock-in

**Problem.** Kiro is an IDE, spec-kit a CLI with its own file layout, Tessl a
framework with generation tags and an MCP server (Böckeler).

**OneSpec.** Specs are plain Markdown in the repository. The skills install
with the host's own tooling (`npx skills add` or the Claude Code plugin
marketplace) and run in Claude Code and Codex.

## Not yet claimable

Keep these out of headlines until they are verified:

- That keeping specs, and the `(spec)` links from code to them, current as
  each change is built beats reconstructing them from the finished code and
  its history afterwards, as onboarding an existing project does. It is
  OneSpec's central bet and is not yet measured.
- Breadth across models: earlier versions of the skills were used with
  Claude, Codex, Muse and Grok, and that use continues; the current skills
  have been observed with one model so far.
- Team-scale use: every observation so far is one developer.

## Messages

For the website, in this order:

1. **Specs that stay true.** Spec-anchored: changes fold back into durable
   specs.
2. **Light by design.** One prose file per change, a bug fix is one patch,
   no tool beyond the skills, and every artifact must earn its place. This is
   the direct answer to the heaviest criticism of SDD tools: markdown
   overload and ceremony out of proportion to the problem.
3. **One step at a time.** Plan only the next increment; review before
   planning again.
4. **Proof, not promises.** Every build records the evidence that it
   delivered.

For the user guide: lead with the build loop and `oneturn`, since they work
without a spec graph, and introduce the graph as what the loop grows into.

Words to use as defined: durable spec, change spec, build, patch, acceptance,
evidence. Avoid "spec as source": OneSpec does not generate code from
specs. "Source of truth" fits only for intent: the durable specs state what
the product should do, and code that disagrees with them is a defect.
