# KGC-ABM

![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-2F6F4E)
![LLM integration: OpenAI-compatible](https://img.shields.io/badge/LLM%20integration-OpenAI--compatible-555555)

> [!IMPORTANT]
> **Peer-review access notice:** This public repository provides a complete
> project overview and demonstration snapshot prepared for peer review. The
> complete source code and supporting data are distributed through the private
> Zenodo review package. Reviewers should use the Zenodo link provided in the
> **Cover Letter** to access and download the complete source code and data.
> Following acceptance, the complete source code will be added to this
> repository, and the supporting data will be made publicly available through
> Zenodo.

KGC-ABM is a knowledge-graph-centered framework for constructing and running
agent-based simulations with evolving world state, agent-specific perspectives,
event-driven interactions, and inspectable execution histories.

![KGC-ABM Animation Viewer showing the Routine Baseline](assets/figures/kgc-abm-viewer-overview.png)

*The Animation Viewer playing a routine community simulation.*

## What KGC-ABM Provides

- Graph-centered representation of agents, places, routes, tasks, events,
  information, beliefs, relationships, and evolving simulation state.
- Event-driven simulation scenarios with checkpoints and branches from a shared
  state.
- Configurable agent runtimes with heuristic, mock, and OpenAI-compatible modes.
- Structured summaries, checkpoints, traces, diagnostics, and Neo4j-compatible
  graph exports.
- A browser-based Animation Viewer for inspecting movement, route conditions,
  events, communication, and changing agent state.

## Demonstrated Scenarios

| Scenario | Description |
|---|---|
| Routine Baseline | Seven days of household routines, travel, work, school, and caregiving. |
| Branch A: Route Disruption | Changed travel conditions and the resulting community response. |
| Branch B: Caregiver Handoff | Caregiving coordination after an assigned caregiver becomes unavailable. |
| Branch C: Public Claim Confirmation | Information exchange and response to a formally confirmed claim. |

These scenarios are presented as a routine baseline followed by event-driven
branches. The branches illustrate how a shared simulated environment can
evolve under changes in mobility, availability, and public information.

## Demonstration Videos

### Routine Baseline

Routine activity across the seven-day community simulation.

https://github.com/user-attachments/assets/b4821a9f-2339-42fe-8b2d-159821f7eff9

### Branch A: Route Disruption

Agent movement and community activity after a route disruption.

https://github.com/user-attachments/assets/144423a2-2b4b-40d8-afac-790a372be315

### Branch B: Caregiver Handoff

Coordination after the scheduled caregiver becomes unavailable.

https://github.com/user-attachments/assets/2eb8aec9-2b24-4d56-9b88-98a2321fe274

### Branch C: Public Claim Confirmation

Communication and response to a formally confirmed claim.

https://github.com/user-attachments/assets/bbf83ffb-c261-4f65-bb86-1366d0bf6fa7

## Project Structure

The complete project release is organized around the following components:

```text
src/kgc_abm/       Core simulation package
configs/           Scenario and runtime configurations
data/scenarios/    Community scenario definitions
examples/          Scenario, checkpoint, interview, and graph examples
viewer/            Animation Viewer, playback tools, and bundle builders
docs/              Architecture, schema, output, and export references
tests/             Core regression and packaging tests
```

The current public snapshot intentionally contains only the project overview,
visual material, and licensing information. The complete implementation and
the files listed above are available in the private Zenodo review package
through the link in the **Cover Letter**.

## Installing the Full Review Package

The Public GitHub repository is a demonstration site rather than the source
distribution during peer review. To install and run the project, download the
complete source archive from the private Zenodo link provided in the **Cover
Letter**, extract it, and run the following commands from the extracted
`KGC-ABM-source-code/` directory.

KGC-ABM requires Python 3.10 or newer.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -e ".[test]"
```

On Linux or macOS, activate the environment with:

```bash
source .venv/bin/activate
```

Confirm the installation with:

```powershell
python -m kgc_abm.cli --help
```

For OpenAI-compatible model integration, copy `.env.example` to `.env` and
provide the endpoint, model, and API key through the documented environment
variables. Never commit a populated `.env` file.

## Quick Start

Run the lightweight smoke scenario without an external API key:

```powershell
python -m kgc_abm.cli run --config configs/smoke_test.yaml --out outputs/smoke_run
```

## Run the Baseline and Event Branches

The following commands use PowerShell. First run the seven-day routine
baseline:

```powershell
python -m kgc_abm.cli run `
  --config configs/formal_7day_llm_clean_schedule_v2_highbudget.yaml `
  --out outputs/routine_baseline `
  --prompt-logging `
  --audit-bundle
```

Then create Branch A, B, and C from the Day 7 checkpoint:

```powershell
python -m kgc_abm.cli run-day8-branches `
  --base-checkpoint outputs/routine_baseline/checkpoints/checkpoint_day_07_end.json `
  --config configs/formal_7day_llm_clean_schedule_v2_highbudget.yaml `
  --out outputs/event_branches `
  --branches A_route_disruption B_caregiver_unavailable C_public_claim_confirmation `
  --prompt-logging `
  --audit-bundle
```

Formal model-backed runs can take substantial time and may incur provider
charges. Natural-language responses can vary across providers and model
versions even when the scenario and output schema remain unchanged.

## Simulation and Inspection

The full implementation supports a routine baseline and event branches from
serializable checkpoints. A completed run can provide:

- run summaries and graph statistics;
- resumable checkpoints;
- time-ordered activity, movement, belief, coordination, maintenance, and
  graph-evolution traces;
- aggregated diagnostics and graph exports;
- compact playback bundles for the Animation Viewer.

Each run writes to the selected output directory. Typical artifacts include
`run_summary.json`, `checkpoints/`, `traces/`, `analysis/`, and `exports/`.
Additional runtime records and materialized graph views are also available for
inspection in the complete source package.

The full output tree may also contain:

- `decision_packets/`: structured runtime records linking graph evidence,
  agent state, and processing results;
- `activated_subgraphs/`: graph views materialized during agent processing;
- `audit_bundle/`: an optional compact bundle of selected summaries and traces.

The Viewer can be started from the extracted source package with:

```powershell
python viewer/tools/serve_viewer.py --port 8765
```

Open <http://127.0.0.1:8765/viewer/> and select the Routine Baseline or any of
the three event branches. The Viewer provides manual playback, speed control,
timeline scrubbing, route overlays, themes, cinematic mode, and agent
inspection. The videos above provide the public demonstration during peer
review; the Viewer source and complete playback tools are included in the
Zenodo source package.

### Viewer Behavior

The Animation Viewer is a trace-driven playback interface. It does not call an
LLM and does not rerun the simulator. Instead, it loads compact animation
bundles generated from completed simulation outputs and presents agent
movement, route state, events, messages, and selected-agent details on the
project map.

### Included Playback Bundles

The Zenodo source package includes four prepared bundles under
`viewer/animation_data/`:

| File | Scenario |
|---|---|
| `routine.json` | Seven-day Routine Baseline |
| `branch_a.json` | Branch A: Route Disruption |
| `branch_b.json` | Branch B: Caregiver Handoff |
| `branch_c.json` | Branch C: Public Claim Confirmation |

These files retain only the structured state, movement, route, message, event,
and chapter data required for playback. They are compact derived artifacts and
are not substitutes for the complete raw simulation outputs distributed in the
Zenodo data package.

### Regenerating Viewer Bundles

Users with compatible KGC-ABM run outputs can regenerate animation bundles with
`viewer/tools/build_animation_bundle.py`. Run the following command from the
extracted source package to inspect the required arguments:

```powershell
python viewer/tools/build_animation_bundle.py --help
```

The generated bundle can then be registered with the Viewer and served through
`viewer/tools/serve_viewer.py`.

### Viewer Data License

The Viewer software is licensed under Apache-2.0. The four derived animation
data bundles are licensed separately under the
[Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).
The corresponding `LICENSE_DATA.txt` is included with the animation bundles in
the Zenodo source package.

The complete source package also includes the following technical references:

- `docs/ARCHITECTURE.md`
- `docs/GRAPH_SCHEMA.md`
- `docs/RUN_OUTPUTS.md`
- `docs/NEO4J_EXPORT.md`

## Verification

From the extracted source package, run the Python regression suite and the
Viewer playback checks with:

```powershell
pytest -q
node viewer/tests/playback_core.test.mjs
```

The complete source package includes the tests and the files required for
these checks. Generated `outputs/`, logs, caches, and populated `.env` files
should remain outside the repository.

## Full Release Access

During peer review, the complete source code and supporting data are available
through the private Zenodo review package. The access link is intentionally
provided through the **Cover Letter** rather than published here.

After acceptance:

1. The complete source code will be added to this repository.
2. The supporting data will be released through the corresponding Zenodo
   record.
3. This README will be updated from the review snapshot to the full public
   release documentation.
4. The repository URL will remain unchanged.

## License

Project materials are provided under the terms in [LICENSE](LICENSE), with
additional attribution information in [NOTICE](NOTICE).
