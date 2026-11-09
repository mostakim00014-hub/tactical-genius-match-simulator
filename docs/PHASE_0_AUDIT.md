# Tactical Genius — Phase 0 Audit

Audit date: 2026-11-09. This audit covers the five assets supplied in the project workspace. No existing game source was changed during the audit.

## Supplied assets

| Asset | Size | Finding |
|---|---:|---|
| Matchday Highlights (TacticMesh engine).html | ~662 KB | Standalone browser matchday/highlights prototype with embedded team pack and a large inline JS bundle. Exact engine revision still needs comparison against modular source. |
| Tactical_Genius_Tactics_Studio.html | ~95 KB | Tactics editor prototype with formations, player roles/instructions, phases and set pieces. Saves locally under localStorage key `tg-tactics-v1`; not connected to career persistence. |
| tacticmesh-main.zip | ~9.5 MB compressed | Modular TacticMesh source, documentation, labs and tests. |
| Tactical_Genius_Player_Database_v2.csv | ~25 MB | 95,254 player rows and 72 columns. |
| football_simulation_optimized_30mb.zip | ~25 MB compressed | Contains a SQLite DB of ~271 MB uncompressed. |

## TacticMesh

The supplied source separates responsibilities across:
- `core.js` and `world.js`: fixed-step simulation and world lifecycle.
- `intelligence.js`: player decision-making.
- `tactics.js`: tactical positioning and team behaviour.
- `physics.js`: movement/contact.
- `rules.js`: match laws and restarts.
- `analysis.js` and `telemetry.js`: analysis and diagnostics.
- Product modules: team data, import/storage, orchestration, cups and reports.

The source is MIT licensed. Preserve the copyright/license notice in substantial reuse. Team packs, images and other assets can have separate permissions and must be checked independently.

### Test execution
Executed `node tools/run-tests.cjs` with Node v22.16.0 from the supplied source tree. **All 52 test programs passed.**

This is a good engineering baseline, not proof of calibrated football realism. The supplied `docs/KNOWN_LIMITATIONS.md` reports a six-match sample with 30 goals, 222 shots and 562 minutes (5.0 goals/game and 35.55 shots/90), and explicitly puts scoring/chance volume on HOLD. It also lists no career system, online multiplayer or hosted simulation API, plus unverified long-run/device coverage.

**Action:** retain the source and tests; integrate/adapt the engine behind Tactical Genius interfaces. Add calibration tests before accepting realism.

## Matchday Highlights and Tactics Studio

The matchday HTML is a standalone prototype with embedded pack data and a large inline JS bundle. Verify its engine fingerprint against the modular TacticMesh source. Prefer one canonical modular source; do not independently edit embedded and modular copies.

The Tactics Studio export is schema version 5. Its design notes correctly describe instructions as behaviour weights rather than guaranteed actions, with decisions depending on attributes, role, tactical familiarity, context, pressure, space, teammates, opposition, fatigue and score. Migrate its state to a versioned shared contract and server persistence.

## Player CSV

Observed scan:
- 95,254 rows; 72 columns; 95,254 unique Player_ID values; no duplicate IDs found.
- Player names and DOBs populated; DOBs parsed successfully.
- Tactical_Genius_Rating values fell within 0–100.
- 7,299 distinct Current_Club values; 10,740 rows have blank Current_Club.

Common_Name, youth stats, Traits, Secondary_Positions and League are frequently missing; many senior career statistics are also missing. The CSV itself does not establish source provenance, collection date, redistribution license, attribute definitions or calibration method. Do not automatically interpret Tactical_Genius_Rating as verified current ability or potential.

**Action:** document provenance, source date, license, field definitions, confidence and conversions before using as authoritative real-world data. Distinguish sourced and generated players.

## SQLite database

The archive contains `football_simulation_optimized.db`, 271,237,120 bytes uncompressed. `PRAGMA integrity_check` returned `ok`; `PRAGMA foreign_key_check` returned no violations.

| Table | Rows |
|---|---:|
| matches | 306 |
| events | 144,183 |
| shots | 9,439 |
| players | 1,126 |
| teams | 18 |
| lineups | 13,505 |
| player_match_stats | 12,155 |
| player_match_metric_values | 301,969 |
| player_match_cards | 1,428 |
| shot_freeze_frame | 13,535 |
| tactical_shapes | 680 |
| match_momentum | 306 |

Source split:
- StatsBomb Open Data: 34 matches, 137,765 events.
- FotMob: 272 matches, 6,418 additional key events plus match/player statistics.
- Derived: separate source category for calculated metrics.

The StatsBomb matches span 2023-08-19 to 2024-05-18 and are associated with 1. Bundesliga (source competition ID 9).

The shot_freeze_frame table contains 13,535 player-position records attached to 908 shot events across those 34 matches (5–20 players per shot). This is useful shot-context information, but **does not establish that the complete StatsBomb 360 event-frame dataset is present**. The current schema has a shot-specific freeze-frame table but no general per-event 360 frame/visible-area store. Full 360 ingestion remains outstanding.

Keep StatsBomb, FotMob and derived values separate by provenance. Do not imply FotMob data is StatsBomb data.

## Risks and Phase 0 remaining work

1. Select one canonical match engine source and compare the HTML build fingerprint.
2. Calibrate scoring/chance volume and validate full-match distributions.
3. Obtain and ingest the actual StatsBomb 360 files; preserve visible areas and event frames separately from shot freeze frames.
4. Establish provenance and rights for the player CSV and FotMob-derived data.
5. Normalize player attributes into a documented canonical model.
6. Migrate Tactics Studio schema v5 to shared versioned contracts.
7. Replace local-only saves with a persistence adapter.
8. Design authoritative server-side multiplayer state.
9. Inspect the repository and establish CI, project structure and migration plan.

### Exit checklist

- [x] Inventory supplied assets.
- [x] Inspect TacticMesh architecture and license.
- [x] Run supplied TacticMesh tests (52/52 pass).
- [x] Inspect Tactics Studio save/export model.
- [x] Inspect player CSV schema and basic quality.
- [x] Inspect SQLite integrity, source split and coverage.
- [ ] Compare embedded matchday engine fingerprint with source.
- [ ] Complete StatsBomb 360 source/coverage audit.
- [ ] Finalize canonical contracts from the actual source repository.

The local asset audit is complete. The remaining checks are explicit follow-up tasks, not assumed completed work.
