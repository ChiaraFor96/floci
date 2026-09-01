## Why

Floci implements Lake Formation resource registration and permission lifecycles, but clients cannot update an existing registration through the AWS `UpdateResource` operation. This blocks SDK-driven workflows that reconcile the registration role after creation.

## What Changes

- Add AWS REST JSON-compatible routing and handling for Lake Formation `UpdateResource`.
- Update the role ARN of an existing registered resource without replacing its identity or unrelated state.
- Return AWS-compatible errors for missing resources and invalid requests.
- Add automated SDK-level coverage for successful updates, isolation, and failure cases.

## Capabilities

### New Capabilities

- `lakeformation-update-resource`: Update an existing Lake Formation resource registration through the AWS API.

### Modified Capabilities

None.

## Impact

- Affects the Lake Formation controller, service, request models, and persisted resource state.
- Extends the existing Lake Formation REST JSON protocol surface without adding a custom endpoint.
- Requires focused service tests and AWS SDK integration coverage.

## Upstream Readiness

- No open upstream issue or pull request currently covers `UpdateResource`.
- Closed upstream PR #1622 included an `UpdateResource` attempt but used AWS JSON 1.1 routing. The replacement contribution must use the current REST JSON Lake Formation controller and SDK-level verification.