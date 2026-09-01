## ADDED Requirements

### Requirement: Update a registered Lake Formation resource
Floci SHALL implement the AWS Lake Formation REST JSON `UpdateResource` operation for an existing registered resource.

#### Scenario: AWS REST JSON request routing
- **WHEN** an AWS SDK client sends `POST /UpdateResource` without an `X-Amz-Target` header
- **THEN** Floci routes the request to the Lake Formation update operation

#### Scenario: Update the registration role
- **WHEN** an AWS SDK client updates a registered resource with a valid resource ARN and role ARN
- **THEN** the operation succeeds and `DescribeResource` returns the new role ARN

#### Scenario: Preserve omitted registration values
- **WHEN** an update omits an optional registration value
- **THEN** the resource retains its previously stored value for that field

### Requirement: Validate update requests with AWS-compatible errors
Floci SHALL reject invalid or unresolved update requests through its existing AWS error mapping.

#### Scenario: Resource does not exist
- **WHEN** a client updates a resource ARN that is not registered in the request Region
- **THEN** Floci returns the Lake Formation entity-not-found error expected by AWS SDK clients

#### Scenario: Required value is invalid
- **WHEN** a client sends an update with a missing or invalid required ARN
- **THEN** Floci returns an AWS-compatible validation error and does not mutate resource state

### Requirement: Preserve resource isolation and persistence
Floci SHALL update only the resource identified in the caller's Region and SHALL persist the update through `StorageFactory`.

#### Scenario: Same ARN exists in another Region
- **WHEN** a resource is updated in one Region and the same ARN is registered in another Region
- **THEN** only the resource in the request Region changes

#### Scenario: Persistent storage reload
- **WHEN** an updated resource is reloaded using a persistent storage mode
- **THEN** the updated registration values remain available