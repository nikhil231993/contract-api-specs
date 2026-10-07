# contract-api-specs

Dummy OpenAPI 3.0 specs used to try out MCP-driven contract-test generation.

## Layout

```
specs/<service>/<version>/openapi.yaml
```

| Service   | Versions   | Notes                                                        |
|-----------|------------|--------------------------------------------------------------|
| accounts  | v1, v2     | v2 paginates `GET /accounts`, adds `POST /accounts`, Money balances |
| payments  | v1, v2, v3 | v2 adds Idempotency-Key + cancel; v3 Money amounts + refunds |
| customers | v1         | create / get / PATCH (merge-patch)                           |
| cards     | v1, v2     | v2 block needs a reason; adds unblock and limits             |

Each spec's `info.description` lists the breaking changes from the previous version,
so an agent can generate version-specific contract tests.

## Conventions

- One self-contained file per version (all `$ref`s are internal `#/components/...`).
- `info.version` matches the folder (`v2` → `2.x.x`).
- `operationId` stays the same across versions for the same logical operation.
