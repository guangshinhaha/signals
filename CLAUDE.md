# Signals

Signals looks after a live product. The product sends redacted feedback, exceptions, and a release SHA. Signals groups them by cause. The pilot accepts one, and an issue opens in that product's repo. After the next release, Signals closes the cause if it went quiet and reopens the same issue if it did not.

Pilots and product ops use it. The people using the host product do not. One self-hosted instance covers many products.

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
