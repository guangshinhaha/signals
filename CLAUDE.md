# Signals

Signals looks after a live product. An agent turns feedback, exceptions, and releases into tasks, then checks that the cause went quiet after the release. A person owns the brief and the policy, and reviews a sample. The split with gantry, shinstack, and shinfactory is in `README.md`.

One self-hosted instance covers many products. The people using a host product do not open Signals.

## Commands

TODO: no scripts yet. Add the real dev, test, and check commands here when they exist.

## Gates

Hooks and CI come from gantry (`lefthook.yml`, `.github/workflows/ci.yml`).
A task is done only when the pre-push checks pass. Never use `--no-verify`.

CI runs every check even when an earlier one fails, so read the whole run, not
just the first red step. With `node:test`, tests that share state (DB, ports,
files) collide under the default parallelism: pass `--test-concurrency=1`.

## Working style

If the shinstack plugin (github.com/guangshinhaha/shinstack) is installed, follow `shinstack:craft`, which its session hook loads. If it isn't installed, ignore this section.

## Review rules

- Auth, permissions, personal data and deploy scripts: a human reviews line by line.
- UI: review by screenshot.

## Knowledge

- Architecture: docs/architecture/
- Decisions: docs/decisions/
- Solved problems and gotchas: docs/solutions/

<!-- shinstack:begin -->
## shinstack
shinstack-mode: supervised

Before a PR:
- none found

Decisions: `docs/decisions/`. Solved bugs: `docs/solutions/`.
<!-- shinstack:end -->
