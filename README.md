# solo-intake

The Intake cell: its own API (Railway, serverless) and its own Neon
database, made from `cell.yaml` with [solo-cell-kit](../solo-cell-kit). It
reuses empire-platform for identity, authorization, executions and audit over
HTTP with the caller's token.

| | |
|---|---|
| API | Railway project `solo-intake`, service `intake-api` (Serverless) |
| Data | Neon project `solo-intake`, database `intake` |
| Registry | solo-empire `repository-catalog/registries/online-systems.yaml` (`intake`) |

## API

Every resource has list, stats, get, get-by-legacy-id, create, patch and delete
(see `cellkit/app.py`). Permissions are `intake.<resource>:read|write|delete`.

| Path | Resource | Filters |
|---|---|---|
| `/v1/sources` | source | `source_key`, `status`, `domain`, `dataset`, `source_kind` |
| `/v1/intakes` | intake | `status`, `domain`, `dataset`, `source_id`, `intake_key`, `idempotency_key`, `lease_owner` |
| `/v1/intake-events` | intake-event | `intake_id`, `event_type` |

## Develop and operate

```bash
uv venv .venv && uv pip install --python .venv/bin/python -e ".[dev]"
.venv/bin/pytest -q
NEON_API_KEY=... .venv/bin/intake-db provision
npm install && railway config apply && railway up --ci --service intake-api
railway scale --service intake-api asia-southeast1-eqsg3a=1 us-east4-eqdc4a=0   # next to Neon and the platform
```

Railway variables: `INTAKE_DB_MODE=hosted`, `INTAKE_DB_URL_API`,
`INTAKE_JWT_PUBLIC_KEY_PEM`, `INTAKE_JWT_ISSUER`, `EMPIRE_PLATFORM_URL`.
