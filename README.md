# warden

warden is a project for an agdev installation (a WFE workflow system with
agents, an Observer and executors on one or more worker hosts). It fills two **core roles**:

| Role | Workflow | What it does |
| --- | --- | --- |
| `recovery` | `recover` | Observer submits an incident (a run whose attempt was interrupted, whose work nothing continues, or whose publication failed). `recovery-agent` investigates it, then continues the run (`wfe run recover`), repairs what stopped it, or hands the question to a person, and records why. |
| `coordination` | `coordinate` | WFE opens a run whenever queued work awaits a decision (a job with none, a deferral that came due, a request to reconsider). `coordination-agent` reads the queue and workers (`wfe coordinate view`), decides what starts, moves, waits or is noted (`wfe coordinate start\|defer\|assign\|note`), and ends. |

Principles kept in the agents' guides: Observer detects, warden decides; a recovery or coordination run is
never itself sent to recovery; a person's hold, answer or placement is not overridden; observations are
dated and unverifiable external effects are said to be unverifiable.

## Summoning it

In a summonplan for `wfe summon apply` (see `docs/summon.md` of agdev's `workflow_editor`), add an entry:

```yaml
- project: warden
  source: https://github.com/iwaag/pj-warden.git
  commit: <exact commit of this repository>
  roles: {recovery: recover, coordination: coordinate}
```

Apply fetches that commit, pushes its history unchanged into your installation's Gitea, registers the
project from `project.yaml` (`id: warden`), obtains a workspace and wires the two roles.

## What is here

- `project.yaml` — the project definition (`id: warden`).
- `devdocs/workflows/recover.yaml`, `coordinate.yaml` — the two role workflows; `freework.yaml` — free-form work in the project.
- `agent/warden.md` (shared), `agent/recover.md`, `agent/coordinate.md` — the agents' guides the workflows name.

## Conditions and limits

- `coordinate` is not pinned to a node: it runs on your installation's default worker. It uses `lane: coordination`,
  so your installation must admit that lane. Both workflows use agent profile `sonnet`; change `execution.profile` if
  your hosts offer other profiles.
- The agents rely on `wfe` commands of the agdev version they were written for (`wfe coordinate …`, `wfe run recover`,
  `wfe exec …`, `wfe observe`). Read agdev's `workflow_editor/docs/coordination.md` and the Observer workflow
  documentation of your installation for the contracts.
- These workflows were developed and run on one installation. Nobody has run them on another; treat them as a
  starting point, not a verified product.
- Not covered: the Observer itself, WFE, executors, or the cluster/host knowledge of any installation; the
  workflows' history of runs (none included); a publication workflow for warden itself.

See [PROVENANCE.md](PROVENANCE.md) for how this repository was made. Licence: CC0 1.0 ([LICENSE](LICENSE)).
