---
title: "Made TaskPilot scale past 30k tasks"
date: 2026-06-04
category: system
venture: personal
tags: [taskpilot, infrastructure, performance]
status: shipped
visibility: public
---

TaskPilot was getting laggy. A background loop had quietly let the task queue grow past 30,000, and the app was loading everything into memory on every view open.

Rebuilt the data path so size doesn't matter anymore. Lists page in as you scroll. The sidebar counts are pre-computed. Nothing scans more than it needs to. The app feels instant again, with headroom past 100,000 tasks before anything starts to bend.

If you're shipping a local-first app on a single-file database, these are the cheapest five things you can do — and they age well.
