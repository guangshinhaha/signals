# Segments

An agent runs this loop. A person writes the rules the agent follows, and looks at a sample afterward. The fourteen-step list was a human process. This page replaces it with four agent operations and two files a person owns.

This page does not choose tools.

## What an agent does

Four operations. Each one is readable and writable through an API. A screen, if one exists, shows the log of these operations. Work does not wait on the screen.

1. **Take an event.** A feedback note, an exception, a release, or a later kind of fact arrives. The call is safe to retry. The same source id does not create a second event. The stored event carries the product id, the source id, the release, and the working text. The working text has no name, email, or IP. Labels (kind, severity, area) are fields on the event, written by the agent that took it.

2. **Attach it to a cause.** The agent puts the event on an existing cause or opens a cause. The submitter does not choose the group. A cause has a stable id, a count, and the releases it was seen on.

3. **Open a task.** When the policy says the cause is worth work, the agent opens one task in the product's tracker and keeps the link on the cause. The task states the user outcome, the count, the window, the source ids, how to see the failure, how confirm will decide it is fixed, and what is out of scope. It does not prescribe a patch. A second event on the same cause updates that task. It does not open another one.

4. **Confirm after a release.** A release event names the SHA now serving. The agent re-reads each open cause against that SHA. Quiet closes the cause and the task. Still firing reopens the same task.

Changing the product (the code, the merge, the deploy) is not one of these operations. shinfactory decides when an open task runs and which agent runs it. gantry runs the machine checks. shinstack tells that agent how to work. Signals stops at the task and the confirm.

## What a person owns

Two files, read by the agent on every operation. Plus a sample, after the fact.

**Brief.** What the product is, and the jobs that are actually on. The agent does not file a task for a job the brief says is off. The agent does not change the brief.

**Policy.** When to open a task, when to wait, and when to stop and ask. The starting policy is:

- Open a task when a cause is seen again, or when the cause is data loss, an auth failure, or a fully broken main path.
- Wait on a single sighting that is none of those.
- Never put a name, an email, or an IP in the task.
- Never change the brief.
- Ask a person before sending a message to a named reporter, and before deleting a person's data.

A person reviews a sample of tasks the agent opened and of changes that merged. A person can void a cause. A person is not in the path of each event.

## Where the old steps went

| Old step | Now |
|---|---|
| Register | A product id on every event. One record, not a stage. |
| Capture, normalize | Take an event. |
| Hold identity apart | A rule of take-an-event. The original stays in the host product. |
| Enrich | Fields on the event. |
| Group | Attach to a cause. |
| Scope | The brief. The agent reads it. |
| Surface | A log an agent can query. |
| Judge | The policy. |
| Route | Open a task. |
| Change the product | Outside Signals. |
| Confirm | Confirm after a release. |
| Reply | The agent may draft it. The policy says to ask before it is sent. |
| Archive | The events, the causes, and the decisions. Retention is a property of those records. |

## Pulse

[guangshinhaha/pulse](https://github.com/guangshinhaha/pulse) (private, PRD v0.1, 18 Jun 2026) and [shiipeii/pulse](https://github.com/shiipeii/pulse) (public, PRD v0.2, 24 Sep 2026) are the feedback path for the first two operations. A channel is a config row. A product is a `products` row. Intake is idempotent on `source_msg_id`. A model then labels the row and attaches it to a theme.

Pulse keeps the raw text. This page keeps the original in the host product and passes working text onward. Pulse v0.2 puts a PM on each label before it counts. This page puts that judgment in the policy. Pulse names a Jira draft, and later a draft pull request, as the way out. This page names a task and stops.

Use of the Pulse code is not decided here.

## Left out

Deciding to start a product stays a brainstorm between the pilot and an AI. A support desk, with a ticket per person, stays out.
