# Tactical Genius — Initial Architecture Decisions

## ADR-001 — Browser-first
Build a responsive browser game first. Keep packages portable for future desktop/mobile shells.

## ADR-002 — Real and generated world
Support sourced real-world entities and generated entities. Provenance must remain explicit; real-world data is a career starting point, not an automatic live sync.

## ADR-003 — Multiplayer by design
Design career membership, server authority, commands, deadlines and audit history into the backend from the start. Deliver private shared careers before public leagues.

## ADR-004 — Hybrid intelligence
Combine empirical distributions, player attributes/tendencies, tactical rules and contextual decision-making. Avoid both rigid scripted behaviour and per-action LLM calls. Runtime uses local versioned models and deterministic simulation logic.

## ADR-005 — GitHub is not the live match database
GitHub stores source, specifications, tests, CI and small versioned artifacts. PostgreSQL stores live game-world state; object storage holds large datasets/model packages. No raw GitHub fetches during match ticks.

## ADR-006 — One shared deterministic engine
Maintain one match-engine package for browser workers and authoritative server workers. Multiplayer server results are authoritative.

## ADR-007 — Modular monolith first
Start with a TypeScript monorepo and modular backend. Split services only when profiling and operational needs justify it.

## ADR-008 — Defer paid infrastructure
Use local development and suitable free/low-cost tiers. Choose paid hosting only after data size, concurrency and performance are measured.
