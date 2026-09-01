## Why

Floci implements Lake Formation resource registration and permission lifecycles, but clients cannot update an existing registration through the AWS `UpdateResource` operation. This blocks SDK-driven workflows that reconcile the registration role after creation.

## What Changes

- Add AWS-compatible routing and handling for Lake Formation `UpdateResource`.
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
- Extends the existing Lake Formation AWS JSON protocol surface without adding a custom endpoint.
- Requires focused service tests and AWS SDK integration coverage.