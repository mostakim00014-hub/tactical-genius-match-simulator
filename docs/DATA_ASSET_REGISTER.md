# Tactical Genius — Data and Asset Register

| Asset | Current role | Provenance/status | Action |
|---|---|---|---|
| TacticMesh source ZIP | Match engine reference/reusable source | MIT source license; pack/image permissions separate | Preserve notices; compare engine version; adapt behind game contracts. |
| Matchday Highlights HTML | Matchday UI/engine prototype | Embedded fictional team pack identifies CC0; exact engine fingerprint to verify | Select canonical engine source; make standalone HTML a generated build artifact if still needed. |
| Tactics Studio HTML | Tactics UI prototype | Export schema v5; localStorage persistence | Migrate to shared versioned tactics contract and career persistence. |
| Player CSV v2 | Candidate player seed data | 95,254 rows/72 fields; source/license/methodology not established by file | Document provenance, license, date, definitions and attribute conversions. |
| Optimized SQLite DB | Historical event and player statistics | 34 StatsBomb matches, 272 FotMob matches, derived data separately tagged | Preserve source separation; review use/terms; extend 360 ingestion. |
| Full StatsBomb 360 | Future context dataset | Not proven present in current DB | Download available 360 files and preserve per-event frames/visible areas. |

## Field policy

Never overwrite provider values with normalized values. Store transformations separately with source IDs, source record IDs, transform version and confidence. Tactical Genius estimates must not be represented as direct StatsBomb observations.
