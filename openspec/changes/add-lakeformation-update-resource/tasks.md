## 1. Confirm AWS Contract

- [ ] 1.1 Verify the REST JSON `POST /UpdateResource` request path, request fields, optional-field semantics, response shape, and error codes against the AWS SDK model
- [ ] 1.2 Confirm the route does not use the AWS JSON 1.1 `X-Amz-Target` dispatch rejected in upstream PR #1622
- [ ] 1.3 Add failing service tests for role updates, omitted optional values, missing resources, and Region isolation

## 2. Implement Resource Updates

- [ ] 2.1 Add `UpdateResource` request and response models using existing Lake Formation model conventions
- [ ] 2.2 Extend `LakeFormationStorage` and its `StorageFactory`-backed implementation with an atomic update operation
- [ ] 2.3 Implement service validation and merge behavior without changing the resource key or unrelated state
- [ ] 2.4 Add the thin REST JSON `POST /UpdateResource` controller route using existing parsing and AWS exception mapping

## 3. Verify Compatibility

- [ ] 3.1 Run `./mvnw test -Dtest=LakeFormationServiceTest`
- [ ] 3.2 Add and run an AWS SDK integration lifecycle test in `LakeFormationIntegrationTest` that verifies REST JSON routing
- [ ] 3.3 Verify persistence behavior with the relevant storage-mode test coverage

## 4. Document and Validate

- [ ] 4.1 Add `UpdateResource` to `docs/services/lakeformation.md`
- [ ] 4.2 Run `./mvnw test -Dtest=LakeFormationIntegrationTest` and the full `./mvnw test` suite