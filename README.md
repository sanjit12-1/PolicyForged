# PolicyForge

PolicyForge is an agentic, multi-agent policy simulation sandbox. It lets users test policy interventions against synthetic populations and examine intended and unintended social consequences across different starting conditions.

## Overview

Static policy analysis can describe direct costs and benefits while missing second-order effects: how resource access can influence stress, trust, support, cooperation, compliance, and relocation over time. PolicyForge explores those interactions with seeded synthetic agents, bounded behavioral rules, configurable policy shocks, and repeated neighborhood interactions.

PolicyForge is a decision-support and policy-testing system, **not a prediction engine**. Its behavioral variables and outcomes are simulated assumptions, not measurements of real people or forecasts of real-world behavior.

## Key Features

- Multi-agent policy simulation with seeded, reproducible runs
- Balanced City, Unequal City, and High-Density City population presets
- Chennai Census 2011-anchored synthetic sample with observed population-scale context
- Water, housing, energy, subsidy, and transport interventions
- Optional primary policy plus one companion policy
- Resource access and inequality analysis
- Stress, trust, satisfaction, policy support, compliance, cooperation, and relocation metrics
- Unintended-consequence scoring
- Income-group impact analysis
- Chennai observed-data provenance and ward-targeted simulation support
- Browser-session results for stateless deployments
- Optional Gemini-assisted policy interpretation with a rule-based fallback

## How It Works

```text
Next.js Frontend
        ↓
FastAPI Backend
        ↓
Simulation Engine
        ↓
Synthetic Agents + Policy Effects
        ↓
Behavioral Interaction
        ↓
Metrics / Population Impacts
        ↓
Policy Assessment
```

The engine creates a seeded synthetic population, applies the configured policy shock once, and advances bounded behavioral state through repeated rounds. Agents interact within neighborhoods, sharing resources or responding to scarcity. The API returns baseline metrics, a round-by-round timeline, final metrics, income-group impacts, and an unintended-consequence score. Chennai ward outputs are explicitly labeled simulation outputs; observed data is used for context and population-scale anchoring only.

## Architecture

```text
frontend/
  app/                    Next.js routes for the dashboard, simulator, scenarios,
                          results, learning, evidence, map, planner, and about pages
  components/             Shared navigation and evidence components
  lib/api.ts              Typed browser API client and simulation data types
  package.json            Frontend scripts and dependencies

backend/
  app/main.py             FastAPI application, access gate, routes, and lifecycle
  app/core/models.py      Pydantic request and validation models
  app/services/
    simulation.py         Population generation, policies, interactions, metrics
    policies.py           Supported policy registry and parameter defaults
    observed_data.py      Chennai observed-data and calibration anchor helpers
    wards.py               Chennai ward boundary and profile helpers
    ai_policy.py          Optional AI interpretation and recommendations
  app/db/store.py         Local SQLite persistence for non-session-only runs
  api/index.py            Vercel FastAPI entry point
  data/chennai/           Observed metrics and source catalog
  tests/                  Backend simulation and observed-data tests

ARCHITECTURE.md           Detailed data and execution overview
CALIBRATION.md            Calibration scope and limitations
DEMO.md                   Demo notes
LIMITATIONS.md            Known limitations
PLAN.md                   Project plan
VERCEL_DEPLOYMENT.md      Vercel deployment notes
docker-compose.yml        Local API and frontend container configuration
vercel.json               Vercel routing configuration
```

## Available Scenarios / Policies

The current policy registry contains:

- Water Rationing
- Water Service Restoration
- Rent / Zoning Change
- Public Subsidy Change
- Energy Service Restoration
- Energy Rationing
- Public Transport Subsidy

The scenario library provides starting points for water stress, affordable housing, public transport subsidy, and energy rationing. Policy parameters are validated and bounded by the backend.

## Population Presets

- **Balanced City**: mixed income distribution, moderate baseline inequality, and moderate resource access and trust.
- **Unequal City**: a larger low-income share, lower baseline resource access and trust, and higher inequality.
- **High-Density City**: a denser synthetic city with its own income mix, resource range, and neighborhood interaction setting.
- **Chennai Census 2011 anchored synthetic sample**: uses observed 2011 Chennai population scale as context while keeping agent attributes and behavior synthetic. It also supports synthetic ward assignment and targeted ward simulation.

These presets change the generated population characteristics. They are not labels layered on top of one identical population.

## Running Locally

The local development API uses port `8001`, and the Next.js frontend uses port `3000`.

### macOS/Linux backend

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m uvicorn app.main:app --reload --port 8001
```

### Windows backend

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m uvicorn app.main:app --reload --port 8001
```

### Frontend

In a second terminal:

```bash
cd frontend
npm install
npm run dev
```

Open <http://localhost:3000>. The frontend defaults to `http://localhost:8001` for the local API. To override it, copy the example configuration and set `NEXT_PUBLIC_API_URL` in `frontend/.env.local`; do not commit that file.

Verify the backend with:

```bash
curl http://localhost:8001/health
```

The expected response is `{"status":"ok","service":"policyforge-api"}`. Optional Gemini interpretation and the access gate use backend-only variables documented in `backend/.env.example`; never place those secrets in frontend configuration.

## Testing

The backend includes tests for reproducibility, policy validation and effects, one-time policy shocks, compliance, API catalogs, simulation lifecycle, assessment, calibration fail-closed behavior, AI fallback behavior, observed data, and Chennai ward outputs.

```bash
cd backend
python -m pytest
```

Frontend TypeScript validation:

```bash
cd frontend
npx tsc --noEmit
```

The frontend production build can be checked with `npm run build`.

## Reproducibility

Each simulation accepts a random seed. Identical configuration, population preset, policy parameters, rounds, neighborhoods, and seed produce reproducible results. Changing the population preset changes the generated income distribution and agent characteristics, which can change the final outcomes even when every other input is held constant.

## Important Design Note

PolicyForge is a simulation and decision-support tool. Its outputs are simulated outcomes based on the implemented behavioral model and should not be interpreted as real-world forecasts.

## Recent Fixes

- Fixed stale simulation results when switching population presets.
- Changing Balanced, Unequal, or High-Density now clears the previous result before a new simulation is run.
- Verified that the selected population preset reaches the backend correctly.
- Verified that Balanced, Unequal, and High-Density generate different populations and outcomes.
- Investigated and verified policy and stress calculations.
- Frontend TypeScript validation passes.

These checks are development and hackathon validation, not production validation.

## Project Status

PolicyForge is a hackathon/MVP project. The current implementation is intended for experimentation, demos, and transparent inspection of synthetic policy assumptions.

## Future Improvements

- Stronger empirical calibration with valid observed behavioral targets
- Richer historical and administrative datasets
- More validated behavioral parameters
- Uncertainty and sensitivity analysis
- A larger policy library
- More expressive agent interaction models
- Real-world validation studies

These are future directions, not existing capabilities of the current MVP.

## Additional Documentation

- [Architecture](ARCHITECTURE.md)
- [Calibration](CALIBRATION.md)
- [Demo notes](DEMO.md)
- [Limitations](LIMITATIONS.md)
- [Project plan](PLAN.md)
- [Vercel deployment](VERCEL_DEPLOYMENT.md)
