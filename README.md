# signals

Signals looks after a live product. An agent turns feedback, exceptions, and releases into tasks, then checks that the cause went quiet after the release.

The people using the host product never open Signals. A pilot owns two files the agent reads: a brief (what the product is, and which jobs are on) and a policy (when to open a task, when to wait, when to ask). A person reviews a sample of what the agent opened and of what merged, and can void a cause. A person is not on the path of each event.

## How signals, gantry, shinstack and shinfactory fit together

Each of the four works alone. When they are used together, they do not overlap.

| Tool | Answers | Owns | How it acts |
|---|---|---|---|
| [signals](https://github.com/guangshinhaha/signals) | What outside fact becomes a task, and did the fix hold? | Events from a live product, the cause that groups them, the task it opens, and the confirm after a release | An agent reads and writes an API |
| [gantry](https://github.com/guangshinhaha/gantry) | Did it pass? | Local hooks, CI checks, the secret scan and the required `gantry / ci-ok` check | Runs checks with no judgment, and can block a merge |
| [shinstack](https://github.com/guangshinhaha/shinstack) | How should the agent work, and may it act? | Principles, playbooks, review depth, PR format and approval modes | Advises the agent |
| [shinfactory](https://github.com/guangshinhaha/shinfactory) | What runs next, and who runs it? | Stage order, retries, provider and model, and merges inside a factory order | Runs the loop once a task exists |

To place a new rule, ask these in order and stop at the first yes:

1. Is it a fact from a live product (feedback, an exception, a release), or a check that the cause went quiet after a release? It goes in signals.
2. Can a machine check it with no judgment? It goes in gantry. A check that only one repo needs goes in that repo's own workflow.
3. Does it need a schedule, a stage, or an event inside a factory order that already has a task? It goes in shinfactory.
4. Would it still apply to one person running one agent session with no loop? It goes in shinstack.

Signals opens the task and stops. shinfactory decides when that task runs and which agent runs it. gantry decides whether the checks pass. shinstack tells the agent how to work, and its `approval-modes` decides whether a merge may happen. A merge still needs both a green check and an approval.

gantry, shinstack, and shinfactory state this same split in their READMEs. The reasoning for those three is in [shinstack-vs-shinfactory](https://github.com/guangshinhaha/shinstack/blob/main/docs/decisions/shinstack-vs-shinfactory.md).

## What the agent does

Four operations. The detail is in [docs/architecture/segments.md](docs/architecture/segments.md).

1. **Take an event.** Feedback, an exception, or a release. The same source id lands once. The working text has no name, email, or IP. The original stays in the host product.
2. **Attach it to a cause.** The agent groups events. The person who reported one does not.
3. **Open a task** when the policy says the cause is worth work. A later event on that cause updates the same task.
4. **Confirm after a release.** Quiet closes the cause and the task. Still firing reopens the same task.

## Pulse

[guangshinhaha/pulse](https://github.com/guangshinhaha/pulse) and [shiipeii/pulse](https://github.com/shiipeii/pulse) already take feedback and attach it to a theme. They are not a fourth peer in the table above. Whether signals uses that code is not decided.

## This repo today

There is no app yet. The product brief is [issue #1](https://github.com/guangshinhaha/signals/issues/1). The Node hooks in `lefthook.yml` stay off until a `package.json` exists.
