# contract-api-specs

Dummy OpenAPI 3.0 specs used to try out MCP-driven contract-test generation.
The layout mirrors a flat "bundled specifications" repo: one self-contained file per API version,
each with a metadata file next to it.

## Layout

```
specs/<ApiName>_v<major>.<minor>.yaml            the OpenAPI spec (all $refs internal)
specs/<ApiName>_v<major>.<minor>.metadata.yaml   status, owner, release date, change list
```

| API               | Versions             | Latest (ACTIVE) | Notes |
|-------------------|----------------------|-----------------|-------|
| CMAccounts        | v1.0, v1.1           | v1.1            | v1.1 paginates `GET /accounts`, adds `POST /accounts`, Money balances |
| CMPayments        | v1.8, v1.9, v1.10    | v1.10           | v1.9 adds Idempotency-Key + cancel; v1.10 Money amounts + refunds |
| CMCustomerProfile | v1.0                 | v1.0            | create / get / PATCH (merge-patch) |
| CMCards           | v1.0, v1.1           | v1.1            | v1.1 block needs a reason; adds unblock and limits |

## Picking the latest version

Compare versions **numerically**, not alphabetically. Sorted by name, `CMPayments_v1.10.yaml` comes
before `CMPayments_v1.8.yaml`, but v1.10 is the newest. CMPayments is set up to catch this mistake.
The `status: ACTIVE` field in the metadata file also marks the current version.

## Metadata file

```yaml
apiName: CMPayments
title: Payments API
version: 1.10.0            # matches info.version in the spec
specFile: CMPayments_v1.10.yaml
basePath: /payments/v1
status: ACTIVE             # ACTIVE | DEPRECATED
owner: payments-platform
releasedOn: 2026-03-02
previousVersion: "1.9"
changes:
  - ...
```
