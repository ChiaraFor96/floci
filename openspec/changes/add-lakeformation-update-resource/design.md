## Context

Lake Formation requests are parsed by `LakeFormationController`, validated and executed by `LakeFormationService`, and persisted through `LakeFormationStorage`. Registered resources are keyed by Region and resource ARN. Adjacent register, describe, and deregister operations already define the protocol, error, and storage conventions that this change must preserve.

## Goals / Non-Goals

**Goals:**

- Expose `UpdateResource` through the same AWS JSON endpoint shape as existing Lake Formation operations.
- Update an existing resource atomically in the configured storage backend.
- Preserve account and Region isolation and AWS-compatible errors.
- Prove compatibility with an AWS SDK client.

**Non-Goals:**

- Redesign Lake Formation storage or persistence.
- Add new resource registration fields that Floci does not otherwise model.
- Change register, describe, deregister, or permission behavior.

## Decisions

1. Add a thin `POST /UpdateResource` handler to `LakeFormationController`. It will parse an `UpdateResourceRequest`, resolve the Region from request headers, delegate to the service, and use the existing response and exception mapping. This follows the current controller pattern and avoids protocol-specific logic in the service.
2. Add service and storage operations rather than implementing read-modify-write in the controller. `LakeFormationService` will validate required values and resource existence; `LakeFormationStorage` will replace supported mutable fields while retaining the same key and unrelated `ResourceInfo` state.
3. Use the existing `StorageFactory`-backed Lake Formation resource map. No parallel cache or direct storage implementation will be introduced, so memory, persistent, hybrid, and WAL modes retain consistent behavior.
4. Model the AWS request fields explicitly. The first implementation will update `RoleArn` and any already-supported optional registration flags supplied by the request, while omitted optional values preserve their current values.
5. Add a service unit test and an integration test using the AWS SDK client. The integration test will register, update, describe, and verify the resource in one flow.

## Risks / Trade-offs

- [AWS distinguishes omitted optional fields from explicit false values] -> Use nullable request fields and merge only values present in the request.
- [Concurrent updates could lose unrelated state] -> Put the merge operation behind the storage abstraction and follow its existing synchronization strategy.
- [Controller routing could accept a non-AWS request shape] -> Validate through an AWS SDK integration test, not only direct HTTP calls.

## Migration Plan

The operation is additive and requires no stored-data migration. Existing resource records remain readable. Rollback consists of removing the route and update methods; stored records remain compatible with the previous release.

## Open Questions

- Confirm from the Lake Formation SDK model which optional registration flags are represented by the current `ResourceInfo` model before implementation.