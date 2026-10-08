# contract-api-specs

Dummy OpenAPI 3.0 specs used to try out MCP-driven contract-test generation.
The layout mirrors a "bundled specifications" repo: domain folders, an `apis/` folder per
sub-domain, and one self-contained file per API version with a metadata file next to it.

## Layout

```
<domain>/[<sub-domain>/]apis/<ApiName>_v<major>.<minor>.yaml            the OpenAPI spec (all $refs internal)
<domain>/[<sub-domain>/]apis/<ApiName>_v<major>.<minor>.metadata.yaml   status, owner, release date, change list
```

| Folder                         | API               | Versions          | Latest (ACTIVE) | Notes |
|--------------------------------|-------------------|-------------------|-----------------|-------|
| `customers/accounts/apis/`     | CMAccounts        | v1.0, v1.1        | v1.1  | v1.1 paginates `GET /accounts`, adds `POST /accounts`, Money balances |
| `customers/profile/apis/`      | CMCustomerProfile | v1.0              | v1.0  | create / get / PATCH (merge-patch) |
| `payments/apis/`               | CMPayments        | v1.8 – v1.14 (7 versions) | v1.14 | v1.9 adds Idempotency-Key + cancel; v1.10 Money amounts + refunds; v1.11–v1.14 change refunds (see below) |
| `cards/cardmanagement/apis/`   | CMCards           | v1.0, v1.1        | v1.1  | v1.1 block needs a reason; adds unblock and limits |

Folder depth varies on purpose (`payments/apis/` vs `customers/accounts/apis/`), so nothing should
assume a fixed number of levels.

## Same endpoint in several versions

`/payments/{paymentId}/refunds` exists in **5 versions** (v1.10 – v1.14) with a different contract in
each, so you can tell from the content which version an agent fetched. It does not exist in v1.8 or v1.9.

| Version | Status | Methods | POST body required | POST responses | Recognisable change |
|---|---|---|---|---|---|
| v1.10 | DEPRECATED | POST | amount, reason | 201, 404, 422 | endpoint introduced |
| v1.11 | DEPRECATED | GET, POST | refundType, amount, reason | 201, 404, 409, 422 | `refundType`, 409, GET list |
| v1.12 | DEPRECATED | GET, POST | refundType, amount, reason | 201, 404, 409, 422 | `Location` header on 201, optional `note` |
| v1.13 | DEPRECATED | GET, POST | refundType, amount, reason | 201, 404, 409, 422 | reason `MERCHANT_ERROR`, GET `status` filter |
| v1.14 | **ACTIVE** | GET, POST | refundType, reason | 201, 404, 409, **429**, 422 | `amount` optional for FULL, 429 rate limit |

A contract test written from the wrong version will fail, so the version has to be chosen deliberately.

## Picking the latest version

Compare versions **numerically**, not alphabetically. Sorted by name, `CMPayments_v1.10.yaml` comes
before `CMPayments_v1.8.yaml`, but v1.10 is newer than v1.8 (and v1.11 is the newest). CMPayments is set up to catch this mistake.
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
