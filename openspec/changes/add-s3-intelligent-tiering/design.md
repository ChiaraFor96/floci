## Context

S3 bucket subresources are routed by query parameter in `S3Controller`, implemented in `S3Service`, and stored as part of the `Bucket` model. Metrics configuration CRUD already provides the closest protocol and persistence pattern. Intelligent-Tiering uses the same REST XML style but has its own configuration shape and AWS error names.

## Goals / Non-Goals

**Goals:**

- Implement put, get, list, and delete Intelligent-Tiering configuration operations.
- Match AWS REST XML parsing, response namespaces, status codes, and error behavior.
- Persist multiple configurations per bucket through existing bucket storage.
- Cover lifecycle and isolation through AWS SDK integration tests.

**Non-Goals:**

- Simulate object transitions between storage tiers or storage billing.
- Add lifecycle configuration behavior.
- Refactor unrelated S3 bucket subresources.

## Decisions

1. Mirror the metrics configuration route and service structure. `S3Controller` will dispatch `?intelligent-tiering` for each HTTP verb before generic bucket handling, preserving real AWS endpoint shapes.
2. Add a dedicated Intelligent-Tiering XML model/parser. It will validate the request `Id`, status, optional filter, and tiering entries, and retain enough structure to produce AWS-compatible get and list responses. `XmlParser`, `XmlBuilder`, and `AwsNamespaces.S3` will be used.
3. Store configurations on `Bucket`, keyed by configuration ID, and save mutations through the existing bucket store. This naturally supports all configured storage modes and avoids direct storage implementation access.
4. Treat put as replace-by-ID, list in deterministic insertion order, and delete as scoped to one ID. Missing bucket and missing configuration errors will follow adjacent S3 conventions with the Intelligent-Tiering-specific error code.
5. Add parser unit tests, service lifecycle and concurrency tests, and an AWS SDK integration test. The SDK test will exercise all four operations and validate returned fields rather than only HTTP status.

## Risks / Trade-offs

- [Raw XML preservation can retain invalid or unstable structure] -> Parse and validate into a dedicated model, then serialize through shared XML utilities.
- [Adding a field to persisted buckets can affect old snapshots] -> Keep the field nullable or default it to an empty map so existing data remains readable.
- [List ordering is not guaranteed by AWS] -> Use deterministic storage order for stable tests without making ordering part of the public contract.
- [Concurrent put operations can overwrite the bucket object] -> Follow the synchronization or atomic update approach proven by metrics configuration tests.

## Migration Plan

The bucket model extension is backward-compatible with records that omit the new field. Rollback ignores the additional serialized field; no destructive migration is required.

## Open Questions

- Verify the exact SDK exception code for a missing Intelligent-Tiering configuration against botocore and AWS documentation during implementation.