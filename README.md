# Phantom Consensus

## Team Information
- **Team Name**: CREED
- **Year**: 2nd year
- **All-Female Team**: No

## Architecture Overview

 Phantom Consensus is a linear 4-phase pipeline that turns raw, dirty political data into a stable consensus agreement.

 ```
 Parse → Sanitize → Engine → Output
 ```

 **Phase 1 — Parse** (`src/parsers/`): Four dedicated parsers load each data source — `representatives.json`, `proposals.json`, `objections.json`, and `relations.csv` — into typed dataclasses. No validation or logic here, just clean ingestion.

 **Phase 2 — Sanitize** (`src/sanitizers/`): Raw data is cleaned in a strict order. IDs are normalized to uppercase, proposals are deduplicated (first occurrence wins),  influence values are cast and clamped to 0–100, and ghost references are dropped with warnings logged.

 **Phase 3 — Engine** (`src/engine/`): The core logic runs in dependency order. Relationship scores are computed first, factoring in trust, betrayal probability, and rivalry. These scores drive trojan horse filtering, false friend detection, and alliance identification. Objection weights (severity × influence) determine which proposals are poison pills. Everything feeds into a final `build_consensus()` call.

 **Phase 4 — Output** (`src/output/`): Results are written to `outputs/result.json` with three keys: `final_agreement`, `detected_alliances`, and `flagged_risks`.

 Run the full pipeline with:

 ```bash
 python main.py
 ```
## Description

Data Cleaning: Raw JSON data was parsed into Python dictionaries. Missing numerical values were defaulted to neutral baselines, inconsistent string formats were normalized, and extreme influence score outliers were capped to prevent single-node dominance.

Alliance & Trust Modeling: The ecosystem was modeled as a directed weighted graph. Asymmetric trust dictated edge weights, while betrayal probabilities acted as a decay factor, lowering the effective trust score of unreliable nodes. Hidden alliances were identified using graph clustering techniques.

Prioritizing Proposals: Proposals were ranked via a net consensus score. This was calculated by summing the influence-weighted support and subtracting the product of each objector's influence and their specific objection severity.

Consensus Strategy: To ensure stability, "Trojan Horses" were flagged by detecting severe objections from highly trusted outlier nodes despite broad surface-level support. "Poison Pills" were filtered out by dynamically simulating their negative impact on network-wide trust prior to approval.

**Note:** Please do not change the format or spelling of anything in this README. The fields are extracted using a script, so any changes to the structure or formatting may break the extraction process.
