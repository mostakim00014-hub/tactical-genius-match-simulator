# Tactical Genius — Target Architecture

Status: agreed baseline, to be refined against the existing code.

## Product decisions

- Browser-first responsive game.
- Real-world and generated clubs/players, with explicit provenance.
- Private shared careers and public multiplayer leagues.
- Hybrid intelligence: empirical football statistics + player attributes/tendencies + tactical rules + match context.
- Persistent multi-season football universe.
- Shared canonical player, club, competition, tactic and match contracts across all systems.

## High-level topology

```text
Browser Client (React/TypeScript)
  ├─ Management UI and Tactical Studio
  ├─ Match renderer (evaluate Canvas/PixiJS against current renderer)
  ├─ Local cache
  └─ WebSocket client
          │ HTTPS / WebSocket
          ▼
TypeScript Application Backend
  ├─ Identity and career membership
  ├─ Career/world orchestration
  ├─ Squad, club, contract and transfer services
  ├─ Scouting, training, finance and competition services
  ├─ Multiplayer command validation
  ├─ Match orchestration / simulation workers
  └─ Persistence and event log
          ├─ PostgreSQL: canonical game state
          ├─ Redis later: cache/coordination only if justified
          ├─ Object storage: large datasets and model artifacts
          └─ Logs, metrics and error tracking

Offline data pipeline
  StatsBomb event + 360 ingestion
    → immutable raw data
    → validation/normalization
    → contextual feature extraction
    → statistical model build and calibration
    → held-out evaluation
    → versioned intelligence packages

GitHub: source, specifications, tests, CI, releases and migrations.
```

## Technology baseline

| Layer | Initial direction |
|---|---|
| Language | TypeScript |
| Frontend | React + Vite |
| Match renderer | Canvas/PixiJS evaluation; choose after prototype profiling |
| Match engine | Shared deterministic TypeScript package |
| Backend | Node.js + TypeScript modular monolith |
| API | REST for commands/queries; WebSocket for live updates |
| Database | PostgreSQL |
| Background jobs | Worker process and durable queue |
| Cache | Redis only when profiling/coordination justifies it |
| Large data | Cloudflare R2 or compatible object storage when needed |
| Source control | GitHub monorepo, branch protection, PRs and CI |

Do not subscribe to every service immediately. Keep local development portable and choose hosting after performance and data requirements are measured.

## Monorepo packages

- `apps/web`: browser game.
- `apps/api`: API, identity integration and career commands.
- `apps/sim-worker`: authoritative match/world worker.
- `packages/domain`: canonical entities and invariants.
- `packages/contracts`: API/event schemas and validation.
- `packages/match-engine`: deterministic fixed-step simulation.
- `packages/football-intelligence`: runtime model/tendency lookup.
- `packages/tactics`: versioned tactic schema and engine adapters.
- `packages/world-sim`: fixtures, seasons, AI clubs and career ticks.
- `packages/data-pipeline`: StatsBomb ingestion and model-building.
- `packages/ui`: shared interface components.
- `infra`: local/deployment configuration.
- `docs`: specifications, ADRs and runbooks.

## Runtime rules

### Match engine
- Fixed-step, deterministic simulation with seeded randomness.
- Simulation state is separate from UI rendering.
- Browser single-player can run in a Web Worker.
- Multiplayer official matches run on an authoritative server worker.
- Browser and server use the same engine package/version.
- No GitHub, StatsBomb or other network requests inside a simulation tick.
- Load players, tactics and compatible intelligence before kickoff; use in-memory/local lookups during play.
- Persist result, event log, stats and engine/model versions.

### Hybrid football intelligence
The runtime is not an LLM call on every player action. It is a versioned set of empirical distributions, contextual probabilities, tendencies and calibrated parameters combined with tactical rules and player-specific attributes.

Decisions consider player ability/role/tendencies, tactical phase/instructions, ball and teammate/opponent context, space, pressure, body orientation, fatigue, score and uncertainty. Model artifacts carry provenance, coverage, build version, evaluation results and compatibility version. Updates happen between matches/session boundaries, not per tick.

### Football world
Each career is an isolated persistent world with clubs, players, competitions, calendar, finances, contracts, transfer market, manager assignments and history. Real-world reference data seeds the world; generated entities fill gaps and replenish it. A real-world seed is not automatically synchronized with real events after career creation.

### Multiplayer
- Server-authoritative career state.
- Every command is validated against membership, permissions, deadlines, money and game rules.
- Career advancement is a deterministic tick/round with engine/rules version recorded.
- Matchday readiness and deadlines are career settings.
- Disconnect fallback uses last valid lineup/tactics or configured AI caretaker.
- Transfers are serialized transactions to prevent duplicate signings and race conditions.
- Public leagues need moderation, reporting, rate limits, audit logs and recovery.

## Persistence domains

Identity; careers and membership; world (countries, clubs, competitions, seasons, fixtures); people (players, staff, managers); squads and registrations; contracts; transfers; scouting; training; finance; matches and event logs; multiplayer commands/readiness/audit.

Use stable internal IDs. Keep external source IDs in provenance/mapping tables; never use a StatsBomb ID as the game's primary player key.

## Data provenance

Every imported value should retain source/provider, source record ID, source release/URL, collection date, license status, transformation, confidence and whether it is observed, estimated, derived or generated. Never silently blend generated values with observed values.

## StatsBomb pipeline

1. Discover event, lineup, match and corresponding 360 files.
2. Verify schema/checksums and retain immutable raw files.
3. Normalize IDs, coordinates, time, teams, players, events and possessions.
4. Ingest full 360 event frames and visible areas separately from shot freeze frames.
5. Extract supported context: shot angle/pressure, pass options, defensive density, pressing, spacing, transitions and positional context.
6. Build distributions and decision models with coverage/uncertainty.
7. Evaluate on held-out matches against observed football distributions.
8. Publish immutable versioned artifacts and a manifest.
9. Clients/servers load compatible artifacts before matches and cache them.

Where 360 is absent, fall back to event data and lower the contextual confidence. Never fabricate 360 observations.

## Performance, security and reliability

- Simulation independent of DOM/rendering.
- Workers for simulation and expensive world advancement.
- Batch world operations; use database transactions.
- Append-only match event records with materialized summaries.
- Index by career, season, competition, club, player and match.
- Profile concurrency before adding infrastructure.
- Server validates competitive actions and results.
- No secrets in browser bundles or Git.
- Use idempotency keys, rate limits, audit logs, backups and tested restores.
- Versioned migrations and save migration paths.

## Delivery gates

1. Canonical contracts and local browser prototype.
2. Working single-player career with persistence.
3. Server-run world advancement and match simulation.
4. Private shared careers.
5. Public matchmaking and leagues after security, recovery, moderation and load testing.
