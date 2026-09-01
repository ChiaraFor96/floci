## ADDED Requirements

### Requirement: Manage Intelligent-Tiering configurations
Floci SHALL support AWS S3 put, get, list, and delete operations for bucket Intelligent-Tiering configurations.

#### Scenario: Configuration lifecycle through an AWS SDK
- **WHEN** a client puts a valid configuration, gets it, lists bucket configurations, and deletes it
- **THEN** each response has the AWS-compatible status and shape and the deleted configuration is no longer returned

#### Scenario: Replace a configuration
- **WHEN** a client puts a valid configuration using an existing configuration ID
- **THEN** the new configuration replaces the prior value without creating a duplicate

#### Scenario: Multiple configurations
- **WHEN** a bucket has multiple configurations with distinct IDs
- **THEN** list returns every configuration and get resolves each by its ID

### Requirement: Parse and serialize AWS REST XML
Floci SHALL validate Intelligent-Tiering XML and SHALL emit S3-namespaced XML that AWS SDK clients can deserialize.

#### Scenario: Valid complete configuration
- **WHEN** a request includes an ID, status, filter, and one or more tiering entries
- **THEN** get and list responses preserve the configuration values

#### Scenario: Malformed configuration
- **WHEN** a request contains malformed XML or omits an AWS-required element
- **THEN** Floci returns `MalformedXML` and leaves existing configurations unchanged

### Requirement: Preserve bucket isolation and durable state
Floci SHALL scope configurations to a single bucket and persist them through the configured bucket store.

#### Scenario: Same configuration ID in different buckets
- **WHEN** two buckets store different configurations under the same ID
- **THEN** get, list, and delete affect only the addressed bucket

#### Scenario: Missing configuration
- **WHEN** a client gets or deletes an ID that is not configured on an existing bucket
- **THEN** Floci returns the AWS-compatible missing-configuration behavior

#### Scenario: Persistent storage reload
- **WHEN** a bucket with configurations is reloaded in a persistent storage mode
- **THEN** all configurations remain available