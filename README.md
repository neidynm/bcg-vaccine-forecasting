# BCG Vaccine Demand Forecasting

This is part of an End-to-end data pipeline project that is going to:
- ingest WHO/UNICEF immunization data,
- engineers features, and 
- forecasts BCG vaccine demand by country. 

I am planning to includes data quality checks and a FastAPI serving layer.

## Overview

BCG (Bacillus Calmette-Guérin) vaccine demand forecasting is critical for supply chain planning in global health. This project builds a production-ready forecasting pipeline covering:

- Automated ingestion of UNICEF administrative immunization data
- Data quality validation and cleaning
- Feature engineering (lag, rolling, demographic features)
- Dual-model forecasting with possibly Prophet and XGBoost
- REST API for serving country-level forecasts
- Orchestration via Apache Airflow
- Infrastructure as code with Terraform

## Planned Stack

| Layer | Technology |
|---|---|
| Ingestion | Python, httpx, pandas |
| Transformation | dbt (staging → intermediate → marts) |
| Feature Engineering | scikit-learn, numpy |
| Forecasting | Prophet, XGBoost, Optuna |
| Data Quality | Great Expectations |
| API | FastAPI, uvicorn |
| Orchestration | Apache Airflow |
| Infrastructure | Docker, Terraform (AWS ECS + RDS + S3) |
| Package Management | uv |

## Possible Project Structure

```
bcg-vaccine-forecasting/
├── ingestion/              # WHO & UNICEF connectors, extractors, loaders
├── dbt/                    # SQL transforms: staging → intermediate → marts
├── feature_engineering/    # Lag, rolling, demographic feature transformers
├── forecasting/            # Prophet + XGBoost models, backtester, Optuna tuning
├── quality/                # Great Expectations validators and reports
├── api/                    # FastAPI app — /forecast and /health endpoints
├── airflow/                # DAGs for ingestion, training, forecasting, QC
├── infrastructure/
│   ├── docker/             # Dockerfiles and compose files
│   └── terraform/          # VPC, ECS, RDS, S3 modules per environment
├── notebooks/              # EDA, model analysis, reporting
├── tests/                  # unit / integration / e2e
└── config/                 # Settings, model config, logging
```

## Data Sources

- **UNICEF** — Administrative BCG vaccination coverage by country and year (1980–2024)
- **WHO** — Global Health Observatory immunization indicators

The cleaned dataset covers **173 countries** with at least 5 years of reporting, spanning 1994–2024.

## Getting Started

### Prerequisites

- Python 3.11+
- [uv](https://docs.astral.sh/uv/) for package management
- Docker (for local orchestration)

### Installation

```bash
git clone https://github.com/neidynm/bcg-vaccine-forecasting.git
cd bcg-vaccine-forecasting

# Create virtual environment and install all dependencies
uv venv --python 3.11
uv sync --extra dev --extra notebooks
```

### Configuration

```bash
cp .env.example .env
# Edit .env with your Postgres, AWS, and API credentials
```

### Running locally

```bash
# Start Postgres + API + Airflow
make docker-up

# Run ingestion pipeline
make ingest

# Run dbt transformations
make dbt-run

# Train models
make train

# Serve forecast API
make serve
```

### API

Once running, the forecast API is available at `http://localhost:8000`.

```
GET  /health          — health check
POST /forecast        — request a country-level BCG demand forecast
```

Interactive docs: `http://localhost:8000/docs`

## Development

```bash
# Install dev dependencies
make dev-install

# Lint
make lint

# Run all tests
make test

# Run unit tests only
make test-unit
```

## Notebooks

Exploratory notebooks are in [`notebooks/exploration/`](notebooks/exploration/).

| Notebook | Description |
|---|---|
| `DataExploration_bcg.ipynb` | UNICEF BCG data EDA — cleaning, temporal analysis, distributions |

## Infrastructure

Terraform modules are under `infrastructure/terraform/` with per-environment configs in `environments/{dev,staging,prod}/`.

```bash
# Plan infrastructure changes (dev)
make infra-plan

# Apply
make infra-apply
```

## License

MIT
