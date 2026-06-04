---
title: "Made TaskPilot scale past 30k tasks"
date: 2026-06-04
category: system
venture: personal
tags: [taskpilot, infra, performance, sqlite, react]
status: shipped
visibility: public
---

TaskPilot started feeling laggy. Turned out a runaway auto-derive loop had multiplied 9 children into 33,000+ queued tasks, the watcher was iterating 33k unconsumed io files on every tick, and the renderer was fetching everything at once on every view open.

Stopped the bleeding first — quarantined the io backlog, cancelled the duplicate tasks. Then hardened the loop so it can't repeat: cursor pagination plus a 200-row cap on the list endpoint, a `?fields=summary` projection that drops the heavy columns, composite indexes matching the actual ORDER BY clauses, a materialized `task_stats` table for sidebar badges, watcher move-on-ingest so processed files never get re-dispatched, and react-virtual on the high-volume views.

Result: list queries are sub-millisecond at any task count, the app feels instant, and the architecture should hold past 100k. If you're running an Electron + SQLite local-first app and starting to feel the lag, these are the cheapest five wins.
