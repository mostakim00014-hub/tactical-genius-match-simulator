# Proposed Repository Structure

```text
tactical-genius/
├── apps/
│   ├── web/                  # Browser management game
│   ├── api/                  # API, identity integration and career commands
│   └── sim-worker/           # Authoritative match/world worker
├── packages/
│   ├── domain/               # Canonical entities and invariants
│   ├── contracts/            # API/event schemas and validation
│   ├── match-engine/         # Deterministic simulation
│   ├── football-intelligence/# Runtime statistical/tactical lookup
│   ├── tactics/              # Versioned tactic schema and adapters
│   ├── world-sim/            # Seasons, fixtures, AI clubs and career ticks
│   ├── data-pipeline/        # StatsBomb ingestion and model building
│   └── ui/                   # Shared UI components
├── data/manifests/           # Dataset manifests, not bulk raw files
├── docs/                     # Specs, ADRs, runbooks and data dictionaries
├── infra/                    # Local and deployment configuration
├── tests/
│   ├── integration/
│   ├── simulation-calibration/
│   └── multiplayer/
└── .github/workflows/        # CI and validation
```

## Branch policy

- `main`: stable reviewed code.
- `develop`: integrated development.
- `feature/<area>-<task>`: bounded implementation tasks.
- `fix/<area>-<issue>`: bug fixes.

Each GPT contributor receives a narrow task, allowed files, required contracts, tests and a branch. All changes go through review and integration. One lead architect owns the master specification. Multiple assistants must not independently overwrite shared core files.

## GPT collaboration protocol

1. Every assistant reads the same versioned project documents.
2. Each task has one owner, scope, branch and acceptance criteria.
3. Each implementation reports changed files, tests run, limitations and branch/commit.
4. One integration owner reviews and merges.
5. Respect each account's usage limits and terms; separate accounts are collaborators, not a mechanism for evading limits.
