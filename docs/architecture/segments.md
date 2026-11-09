# The loop

Signals looks after a live product. Reports arrive through inbound adapters. An agent rates each one, and either waits, opens a task, or asks a person. shinfactory changes the product. Signals then checks whether the cause went quiet.

This page is the accepted shape. It does not choose storage, vendors, or libraries. Deeper specs are separate issues.

## Terms

| Term | Meaning |
|---|---|
| Event | One fact from one source. Safe to send twice. The same source id lands once. |
| Working text | The text an agent may read. No name, email, or IP. The original stays in the host product. |
| Cause | One underlying problem or request. Many events can attach to it. The reporter does not choose it. |
| Task | The issue opened for a cause in the product's tracker. A later event updates that task. It does not open a second one. |
| Brief | What the product is, and which jobs are on. The agent reads it and does not edit it. A job that is off gets no task. |
| Policy | When to open a task, when to wait, and when to ask. |
| Inbound adapter | Turns one raw payload into one event. It does not group, rate, or open a task. |
| Decoder | The per-source piece inside an inbound adapter. A new source is a config row plus a decoder. |
| Outbound adapter | How the agent reads or writes something outside Signals. |
| Fact-check | An outbound look for something that already does the job, before a request becomes a task. |
| Severe class | Lost data, an auth failure, or a broken main path. |
| Second sighting | A second event on the same ordinary cause. That is the bar for opening a bug task. |

## Three kinds

The agent rates the change, not the sentence someone wrote. The rating is the table in shinstack `playbooks/opening-a-pr.md`: how bad a wrong change is, whether its effects can be undone, and whether tests would catch it. Auth, permissions, sensitive data, migrations, shared state, deploy, a public API, or deleting something others depend on is High at any size. An unclear rating goes up.

| Kind | What arrived | Before a task exists |
|---|---|---|
| Bug | Something failed, or a crash | Attach it to a cause. No brainstorm. |
| Low or Moderate request | Someone asked for a solution | Restate the job, fact-check, then rate it. |
| High request | An ask whose change is High | The same restatement, then stop and ask. |

A request that touches auth is High. A three-line bug in one tested module is Low.

## Bugs

Every bug report becomes an event immediately.

An ordinary cause waits. The task opens on the second sighting. A severe class opens a task on the first event.

The task goes to shinfactory. In a supervised repo the factory may still pick it up. The ask is at merge, which `supervised` already means. There is no extra ask on each bug report.

The cause closes when it is quiet after the release that was meant to fix it. A merged change that is still firing reopens the same task.

## Requests

A person asks for a solution. The agent does not build that solution.

Before a task, the agent runs `shinstack:brainstorm`. The pass answers three things: the job they are actually trying to do, whether something already does that job, and the smallest change that would do it.

The fact-check is mandatory on a request. It reads this product's brief and code, the briefs of the other products, and the public web. If a real product already does the job, the outcome is "use that" or "don't build". It is not a new feature.

The repo's `shinstack-mode` then decides the handoff.

| Situation | What happens |
|---|---|
| `auto`, and the change is Low | Open the task. shinfactory builds it. Merge follows the shipping gate. |
| `auto`, and the change is Moderate | Same, with the review depth that table already sets. |
| High, in any mode | Ask before a task is opened. A verifier is not a substitute for that ask. |
| `supervised`, and the change is a request | Ask before the task is opened. |

Moderate requests in an `auto` repo do not ask first. That ask would put a person back on ordinary work.

## Adapters

Inbound decoders named so far: the product's own form, FormSG, a Slack workflow, a note (a boss comment, a mail, or a paste), an exception source, and a release ping. The release ping is what makes confirm possible. Adding one is config plus a decoder. Pulse already uses this shape for form, webhook, FormSG, and email.

Outbound adapters the agent needs: read the brief and the repo, read open tasks so it does not file a duplicate, search outside for an existing product, and write the task. Posting back to the channel that reported it is also outbound. Ask before a message goes to a named person, and before deleting a person's data.

## Plumbing under the kinds

Four operations. Each one is readable and writable through an API. A screen, if one exists, is a log. Work does not wait on it.

1. **Take an event.** This is the inbound adapter.
2. **Attach it to a cause.** This is the bug bar, or the request's restatement.
3. **Open a task.** This is the mode and the risk tier.
4. **Confirm after a release.** A release event names the SHA now serving. Quiet closes the cause and the task. Still firing reopens the same task.

shinfactory decides when an open task runs and which agent runs it. gantry runs the machine checks. shinstack tells that agent how to work, and its mode decides whether a merge may happen. Signals stops at the task and the confirm.

A person reviews a sample of tasks opened and of changes merged, and can void a cause. A person is not on the path of each event.

## Pulse

[guangshinhaha/pulse](https://github.com/guangshinhaha/pulse) and [shiipeii/pulse](https://github.com/shiipeii/pulse) already take feedback and attach it to a theme. A channel there is a config row. Whether Signals uses that code is not decided. Pulse keeps the raw text. This page keeps the original in the host product.

## Specs still to write

The shape above is accepted. These issues turn each part into a spec with fixtures:

- [#2](https://github.com/guangshinhaha/signals/issues/2) the event an inbound adapter must emit
- [#3](https://github.com/guangshinhaha/signals/issues/3) the bug bar, severe classes, and when a task closes
- [#4](https://github.com/guangshinhaha/signals/issues/4) the request path before a task exists
- [#5](https://github.com/guangshinhaha/signals/issues/5) how risk tier and repo mode gate a task
- [#6](https://github.com/guangshinhaha/signals/issues/6) the outbound adapters

## Left out

Deciding to start a product stays a brainstorm, before anyone is using it. A support desk, with a ticket per person, stays out. Storage, vendors, and libraries stay out of this page.
