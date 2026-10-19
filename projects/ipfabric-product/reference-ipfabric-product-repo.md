---
name: reference-ipfabric-product-repo
description: Local path to the IP Fabric product source code repository
metadata:
  type: reference
---

IP Fabric product source code is at `D:\users\daren\repos\ipfabric-product`.

TypeScript/Node.js monorepo. Key locations for propagation zone work:

- `packages/update-device/src/infrastructure/repositories/on-prem-construct/writers/` — one writer per data type; each shows what `msg.*` key paths are read from device JSON and how they map to Postgres tables
- `packages/postgres/src/lib/models/postgres/` — auto-generated Postgres type definitions; field names here = JSON field names in device files (direct spread, no renaming)
- `packages/discovery-shared/` — shared discovery config, fileClient (raw device text → JSON)
- `services/discovery-worker/` — main discovery processing pipeline
- `services/discovery-simulate-api/` — simulation API (potentially relevant to Vinny's snapshot tool)

**How to apply:** When looking up device JSON schema for a specific data type, read the corresponding writer file (e.g. `saveRoutingProtocols.ts` for BGP/OSPF/IS-IS/LDP/RSVP neighbours). The `msg` object in the writer's `Params` type IS the device JSON structure. The Postgres model files confirm field names and types.
