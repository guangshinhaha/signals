# Segments

This page names the segments of the outer loop. It does not choose tools or say what to build first.

Two existing repos cover the feedback half of this list.

- [guangshinhaha/pulse](https://github.com/guangshinhaha/pulse) is private. The PRD is v0.1, dated 18 Jun 2026, owner Tan Guang Shin. Tenant zero is Teacher's Workspace. The P0 build ingests, enriches, clusters, and shows feedback.
- [shiipeii/pulse](https://github.com/shiipeii/pulse) is public. The PRD is v0.2, dated 24 Sep 2026. It keeps that spine and adds a bug and feature loop: the submitter picks a kind, a model may relabel it, a PM confirms, and a reporter is told when a bug is fixed. Drafting a code fix is specified and not built.

Pulse's own names for the early segments are ingest, enrich, cluster, surface, route, and archive. The names below are the ones this repo uses. The Pulse column says what already exists under those older names.

A new feedback channel in Pulse is a configuration row, not a code change. A second product is a `products` row, not a schema change. Signals registration is that same move, one level up: the tenant is the product, and the channel is one way a fact arrives.

## Segment list

### 1. Register

A live product becomes a tenant.

In: the product name, where its tasks go, and who may accept work.

Out: a product id.

Pulse: a `products` row. Teacher's Workspace is the first tenant.

### 2. Capture

A fact arrives from a channel.

In: whatever that channel emits.

Out: a raw payload and a source id.

Pulse captures feedback through a hosted form, a webhook (Tally, Typeform, or Jotform), a FormSG decoder, and email forwarded through a Cloudflare worker. CSV import and chat capture are written down and not built.

Pulse does not capture exceptions, release identifiers, usage counts, or performance scores.

### 3. Normalize

Every source becomes one record. A repeat of the same source id is ignored.

In: the raw payload and the channel configuration.

Out: one stored row, or a refused duplicate.

Pulse: `POST /api/ingest/[channelId]` with a token. Decoders are `passthrough`, `formsg`, and `email`. The dedup key is `source_msg_id`.

### 4. Hold identity apart from working text

The original stays available to a person who is allowed to see it. A later step that calls a model receives only the working text.

In: the normalized record.

Out: a stored original, and a working extract.

Pulse stores `raw_text` and `raw_payload`. The v0.2 PRD adds a submitter email and screenshots, and names a PDPA review before those are used. The Signals brief says a model sees a redacted extract, with no name, email, or IP. How those two rules meet is not decided in this page.

### 5. Enrich

One record gets labels.

In: the working text.

Out: labels, and a reason when a label disagrees with the submitter.

Pulse runs this after the row is saved. A failed label retries. The form still accepts submissions when the model is down. v0.1 sets segment, feature area, type, severity, and sentiment. v0.2 adds the submitter's kind, the model's kind, and a flag when they differ. The kinds are bug, feature, and other.

### 6. Group

Many records become one cause. The person who submitted a record does not choose the group.

In: labelled records for one product.

Out: a theme and a count.

Pulse attaches a record to the nearest existing theme, or starts a theme and labels it. v0.2 groups a bug only with bugs, and a feature only with features. Merge and split are specified in a later phase.

Grouping a feedback record with an exception, a usage count, or a performance score is not in Pulse.

### 7. Scope

A product says which of its jobs are actually on. A cause in a job that is off is not treated as live work.

In: a list the product publishes.

Out: a filter that Group and Route can read.

Pulse has no equivalent. The Signals brief asks for this list because a product can contain features that are built and switched off.

### 8. Surface

A person can see the causes, open one, and search the past.

In: themes and records.

Out: a list and a search result.

Pulse has a dashboard, filters, a drill from a theme to its records, highlights, lowlights, and semantic search. Bugs rank by frequency times severity. Features rank by how many people suggested them.

The Signals brief describes a shorter list: today's causes, with accept, drop, and wait.

### 9. Judge

A person accepts, drops, relabels, merges, or splits. The list does not change the product's tracker by itself.

In: a surfaced cause.

Out: a decision, with who made it and when.

Pulse v0.1 recommends a priority and does not edit a backlog on its own. Pulse v0.2 has the PM confirm or change the kind. The PRD sets a target that the PM keeps at least 90 percent of the model's kinds.

The Signals brief has three outcomes: accept, drop, or wait. A single sighting waits, unless the cause is data loss, an auth failure, or a fully broken main path.

### 10. Route

An accepted cause becomes a task in the product's tracker.

In: the decision and the evidence.

Out: a task id.

Pulse specifies a drafted Jira issue as a later requirement. It is not in the P0 build.

The Signals brief specifies a GitHub issue. The body states the user outcome, the count, the time window, the source ids, how to see the failure, how Confirm will decide the cause is fixed, and what is out of scope. The body does not prescribe a patch.

This page does not choose the tracker.

### 11. Change the product

The task becomes a code change and a release.

In: the task.

Out: a release identifier.

Pulse v0.2 Phase D specifies an AI draft pull request on GitHub, or a prepared change for GitLab. A person reviews, merges, and deploys. That phase is not built. The PRD says it waits on IT approval for repository access.

The Signals brief stops at the accepted issue. shinfactory decides when that issue runs and which agent runs it. gantry runs the machine checks. shinstack tells the agent how to work.

### 12. Confirm

After a release, someone or something decides whether the original cause is quiet.

In: the cause and the release id.

Out: the cause is closed, or the same task is open again.

Pulse v0.2 asks an engineer to mark the theme fixed after the production deploy. A new report after that fix reopens the theme. The check is a person's attestation.

The Signals brief reads the same cause again after the release ping. Quiet closes it. Still firing reopens the same issue.

### 13. Reply

The person who reported the cause hears the outcome.

In: the confirm result, and a contact the product still holds.

Out: one message.

Pulse v0.2 emails the reporter when a bug is fixed. A suggestion sends a notice only if it is released. The Signals brief does not include this segment.

### 14. Archive

The record, the decision, and the task stay findable. A person's data can be removed.

In: the outputs of the segments above.

Out: a retained history, and a deletion when one is required.

Pulse keeps a searchable archive. v0.2 plans an audit log and deletion on request. The Signals brief says the audit trail is the list, the decision, and the issue.

## Left out of the list

Deciding to start a product sits outside these segments. The pilot does that in a brainstorm with an AI, before the product has users.

A support desk, with SLAs and a ticket per person, is out. Pulse v0.1 says so. Interviews and usability tests stay on the research track. Pulse says that too.

## Overlap

Pulse is an implementation of the feedback path through Register, Capture, Normalize, Enrich, Group, Surface, and Archive, with Judge and Route specified and Reply specified in v0.2.

Signals, as briefed in issue #1, uses the same Register and Judge shape, adds Scope, adds exception and release to Capture, and defines Confirm as a re-read of the cause after a release. It stops before Change the product.

Two differences are recorded here and not resolved.

Pulse keeps the raw text and plans to keep the submitter email and screenshots. The Signals brief keeps the original in the host product and sends a redacted extract onward.

Pulse names Jira as the task destination. The Signals brief names GitHub. Change the product is specified inside Pulse Phase D and left to shinfactory in the Signals brief.
