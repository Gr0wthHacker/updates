---
title: "Shipped a memory subsystem for TaskPilot"
date: 2026-06-04
category: system
venture: personal
tags: [taskpilot, memory, ai, sqlite, electron]
status: shipped
visibility: public
---

Added a memory layer to TaskPilot. Drop text, a URL, or a file into the new `/memory` inbox and the app stores it locally; an external runner reads the raw content and returns a summary, tags, extracted entities, and suggested links to related items or tasks. Everything lands in a search-indexed table you can recall from anywhere in the app.

Capture is fast — `Ctrl+K` palette, an `m` hotkey, or the drop dialog. Lookup is fast — virtualized list, filters by status/kind/tag, FTS5 search across summary and raw content. Scalable by construction — cursor pagination on the list endpoint, summary-mode projection for list payloads, a materialized counts table for the sidebar badge, watcher move-on-ingest so the same item can't be enriched twice.

Same local-first model as the rest of TaskPilot: the app makes zero Anthropic API calls. An external Claude Code session does the AI work via a dedicated skill (`/memory-enrich`). Hit `m` from anywhere in the app to drop something in.
