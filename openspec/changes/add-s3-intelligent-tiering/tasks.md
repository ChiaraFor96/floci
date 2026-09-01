## 1. Confirm AWS Contract

- [ ] 1.1 Verify Intelligent-Tiering XML shapes, required fields, status codes, and exception names against the AWS SDK model
- [ ] 1.2 Add failing parser tests for valid configurations, optional filters, multiple tierings, and malformed XML

## 2. Implement Configuration Storage

- [ ] 2.1 Add the backward-compatible Intelligent-Tiering configuration field to the `Bucket` model
- [ ] 2.2 Implement the dedicated XML parser and serializer with `XmlParser`, `XmlBuilder`, and `AwsNamespaces.S3`
- [ ] 2.3 Add put, get, list, and delete service methods following the existing metrics configuration storage and concurrency pattern

## 3. Implement AWS Routes

- [ ] 3.1 Route PUT and DELETE bucket requests with `?intelligent-tiering` to the new service operations
- [ ] 3.2 Route GET requests to get-by-ID or list behavior and return SDK-compatible XML and headers
- [ ] 3.3 Add service tests for replacement, multiple IDs, missing resources, bucket isolation, concurrency, and persisted reload

## 4. Verify Compatibility

- [ ] 4.1 Add an AWS SDK integration test covering the complete configuration lifecycle and returned values
- [ ] 4.2 Run focused parser, service, and integration tests for Intelligent-Tiering and adjacent metrics configuration behavior
- [ ] 4.3 Add the supported operations to `docs/services/s3.md`
- [ ] 4.4 Run the full `./mvnw test` suite and a native package build if controller response models changed