# OneSpec

Spec-driven software development methodology.

## Skills

| Skill | Use it to |
|---|---|
| `onespec` | map a project's specs and where they are not yet matched by code and tests; once per project |
| `oneplan` | review the previous build and plan the next build and its atomic patches |
| `onebuild` | execute a planned build, one atomic commit per patch |
| `onereview` | check spec coverage of what changed |
| `onewrap` | close a change spec: review everything it changed, fold its durable content into the parent spec, retire it |
| `oneturn` | take an unplanned fix or change from report to delivery, to the working tree or as a PR |

`oneplan` and `onebuild` work without a spec graph; adopt them first and grow into the rest.

The [user guide](docs/user.md) explains the concepts and the build loop.

## Install the skills

This repository is a standard skill directory: `skills/<name>/SKILL.md`.

```sh
npx skills add SashaOv/onespec
```

Claude Code users can alternatively add it as a plugin marketplace:
`/plugin marketplace add SashaOv/onespec`.
