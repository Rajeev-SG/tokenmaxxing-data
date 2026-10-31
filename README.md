# tokenmaxxing-data

The public data feed behind **[tokenmaxxing.rajeevg.com](https://tokenmaxxing.rajeevg.com)**.

This repository is machine-written. A job on my Mac replaces the whole branch,
including this file, every five minutes. **Do not edit anything here by hand**,
because the next publish will overwrite it.

## What is here

| File | What it is |
| --- | --- |
| [`current.json`](https://raw.githubusercontent.com/Rajeev-SG/tokenmaxxing-data/main/current.json) | The live packet the dashboard reads. Aggregate coding-agent usage: token totals, cost attribution, subscription vs metered split, top models, and pipeline health. |

That is the entire repository. There is no history to speak of: each publish is
a single commit that replaces the previous one, so the branch always points at
the newest packet and nothing accumulates.

## Who reads it

The dashboard fetches the raw file directly from the browser. That is why this
repository is public: there is no server in between, and no credentials are
involved in reading it.

If you find broken numbers on the dashboard, they come from here, and the fix
belongs upstream in the publisher rather than in this repository.

## What is deliberately not here

This feed is aggregate-only. It contains no prompts, no responses, no code, no
file paths, no task or session identifiers, and no credentials.

The publisher enforces that before every write: it rejects any packet containing
identifier-bearing fields, refuses a privacy block that has been weakened from
`{aggregate_only: true, identifiers: false, content: false, credentials: false}`,
and refuses values like `NaN` that would break the page's JSON parsing. Because
this repository is public, that check is the only thing standing between private
telemetry and a public read surface. It is tested upstream in
`tests/test_public_packet_delivery.py`.

## How it is published

Source of truth: [Rajeev-SG/codex-session-orchestration-analysis](https://github.com/Rajeev-SG/codex-session-orchestration-analysis),
specifically `site/publish_public_packet.py` and the launchd job
`com.rajeev.tokenmaxxing-publisher`.

It pushes over SSH with a deploy key that is scoped to this repository only, so
the credential used here cannot read or write anything else on my account.

## Why not object storage

This feed used to live in Vercel Blob. Blob is billed per operation on the free
tier: two thousand `put`/`copy`/`list` calls a month for the whole account. A
five-minute write loop needs about eight thousand a month, which exhausted the
allowance and got every Blob store on the account suspended for thirty days. A
Git branch has no per-operation quota, and it costs nothing.
