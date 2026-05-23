# pvp-demo

End-to-end market data infrastructure for quantitative research across crypto and synthetic equity/FX markets. Runs 24/7 on a self-hosted Proxmox server.

Screenshots only - source code and data kept private.

## Stack

- **Web app**: Python 3, FastAPI, Jinja, HTMX, TradingView Charting Library, Highcharts
- **Storage**: Delta Lake and RedPanda as Datalake ; and with ClickHouse and Redis Streams as Feature Store
- **Ingestion**: async Python scrapers (multi-exchange, twitter, ...)
- **Featurization**: async Python pipelines (numba/numpy/pandas primitives rewritten to be streaming-friendly - stateful per-symbol buffers maintained in-memory across events, incremental updates instead of batch recompute) (one-server infra, dropped Flink to reduce overhead)
- **Monitoring/Alerting**: Grafana, Prometheus, Matrix/Element
- **Orchestration**: systemd, Ansible, Proxmox, Docker
- **Config**: YAML, Pydantic, SQLModel, sqlacodegen
- **Docs**: Material for Mkdocs
- **Tests** : pytest, pytest-cov
- **Data quality**: Great Expectations
- **Backups** : mc, Proxmox Backup
- **Versioning** : gitea

**Infra** : 1 Dell T350 - 86G RAM - 8vCPU Intel Xeon + 1 Raspberry Pi w/ 4G SIM + 1 APC UPS

## What's inside

A full pipeline ingesting market data from multiple exchanges, storing it in a lakehouse architecture, and serving it through a research web app for strategy exploration.

### Research interface

TradingView-based charting with custom tooling for strategy prototyping. For example, candles can be rendered as tick bars rather than time bars.

![Chart](chart.png)

### Dashboards

Custom dashboards aggregating cross-market data and derived features.

![Dashboards](futures.png)

### Monitoring

Single-pane Grafana view covering ingestion lag, data quality checks, and system health.

![Grafana](grafana.png)

## TODO

- Featurizers checkpoint (resumable/fault-tolerant, restart without recomputing a full table in the Feature Store. Some indicators require state built up from full history, stateful accumulator must dump its state periodically
- Featurizers lazy backfill (add a new column without recomputing a full table in the Feature Store)
- Optimize heavy CPU featurizers
