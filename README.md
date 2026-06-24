# AIMA Gateway Public Protobuf Definitions

## Overview

This repository provides the Protocol Buffer (protobuf) definitions for the AIMA Gateway.

AIMA Gateway is a service designed to facilitate efficient communication between external systems and our AIMA platform using either gRPC or REST.

Its protobuf definitions specify structured data contracts to ensure consistent, reliable, and backward-compatible integrations.

If you are unable to use gRPC, you can also use our REST endpoints, though we recommend using gRPC as it is faster and more efficient.  

## Repository Purpose

- Store and manage protobuf definitions used for communication with the AIMA Gateway.
- Ensure consistent data structures and backward compatibility for customer integrations.

## See also

- Protocol Buffers: <https://protobuf.dev/>
- gRPC: <https://grpc.io/>
- Shared Protocol models: [aima-gateway-protocols-shared](https://github.com/team-sigma-ai/dtp-aima-gw-protocols-shared)

## Contents

The definitions are grouped by API version. Each version is an independent
namespace under `ai.sigmafinancial.aima.<version>.<service>`.

The order for repeated fields on the wire carries no meaning and must not be relied upon.

e.g. if there is a `repeated <type> field = ...` you should not assume that the order of
the elements in the array will be consistent between calls or 
that they reflect the order of the elements in the input.

## Notes

These protocols adhere to the following standards:

- `proto3` syntax throughout.
- Timestamp fields always:
  - Use nanosecond Unix Epoch time except where explicitly stated.
  - Represent UTC.

## Versioning

We use versioning per namespace to ensure controlled updates and backward
compatibility.

### Guidelines

- The server namespace includes the version e.g. `ai.sigmafinancial.aima.v1.generative.services.Generative`
- Breaking changes to an endpoint results in a new version being created for only that endpoint.
- Fields starting with `internal_` are reserved for internal use and should not be relied upon as they can change without notice.

### Example

- If a breaking change is made to `v1.generative.services.Generative.ChatStreaming`,
- A new version will be introduced as `v2.generative.services.Generative.ChatStreaming`.
- Other endpoints in `v1.generative.services.Generative` will remain at `v1` unless further changes are required.

Minor updates, such as adding new fields to responses or adding optional
fields, are backward-compatible and do not result in a new version.

## Deprecation Policy

We only provide support for `N-1` versions: the current version (`N`) and the previous version (`N-1`).

### Process

- When a new version (e.g. `v3`) is released, the oldest version (`v1`) will be deprecated and removed after **three months**.
- Notices will be sent via email to accounts at least three months prior to removal.

### Action Required

- Update to at least the next version (`N-1`), but we strongly recommend upgrading to the latest version (`N`).
- While deprecated endpoints may continue to function, there will be no support for these, and could be removed at any time.

## Compiling

To use the gRPC service definitions, you will also need the shared models.
Ensure that you have set your `protoc` search path to include the parent folder which includes both sets of gRPC definitions.

Example if your folder structure is like the one below, set your search path to include `aima-gateway-protos`.

```text
aima-gateway-protos
|
+---- dtp-aima-gw-protocols-shared
|
\---- dtp-aima-gw-protocols-public
```
