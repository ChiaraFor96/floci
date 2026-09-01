## Why

Floci does not currently support the S3 Intelligent-Tiering configuration operations used by AWS SDK clients. Infrastructure and adapter tests therefore need an external compatibility layer to create, inspect, list, and delete these bucket configurations.

## What Changes

- Add AWS REST XML support for putting, getting, listing, and deleting S3 Intelligent-Tiering configurations.
- Persist configurations per bucket and configuration ID through Floci's storage abstraction.
- Match AWS request parsing, response XML, status codes, and missing-configuration behavior.
- Add AWS SDK integration coverage for lifecycle, multiple configurations, and bucket isolation.

## Capabilities

### New Capabilities

- `s3-intelligent-tiering`: Manage bucket-level S3 Intelligent-Tiering configurations through AWS-compatible APIs.

### Modified Capabilities

None.

## Impact

- Affects S3 REST XML routing, models, service logic, and storage-backed bucket state.
- May require native-image reflection registration for new controller response models.
- Requires focused service and AWS SDK integration tests plus S3 service documentation updates.

## Upstream Readiness

- No matching upstream issue or pull request, open or closed, was found for S3 Intelligent-Tiering configuration CRUD.
- The existing S3 metrics-configuration implementation provides the intended native routing and persistence pattern.