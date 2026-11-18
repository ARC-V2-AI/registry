# ARC-V2 Registry

The official catalog of ARC-V2 services maintained by the `ARC-V2-AI` organization.

Forge reads the catalog from:

```text
https://raw.githubusercontent.com/ARC-V2-AI/registry/main/registry.json
```

## Repository layout

```text
ARC-V2-Registry/
├── registry.json
└── schemas/
    ├── registry.schema.json
    ├── service-entry.schema.json
    └── service.schema.json
```

## Adding a service

Add an entry to `registry.json` under `services`, using the service's unique
`[tool.arc.service].id` as the object key:

```json
{
  "services": {
    "some-service": {
      "repository": "https://github.com/ARC-V2-AI/some-service.git",
      "ref": "main",
      "metadata": {
        "module": "some_service.service",
        "name": "Some ARC Service",
        "description": "What the service provides",
        "restart": "on-failure",
        "health": "restart",
        "depends": [],
        "system_dependencies": []
      }
    }
  }
}
```

`metadata` mirrors every field in `[tool.arc.service]` except `id`, which is
the registry key. The ID declared in the service repository must equal that
key. Forge validates the downloaded repository's declaration against the
registry before installing it; mismatches are rejected.

### Metadata fields

- `module`: importable module containing the service implementation.
- `name`: display name.
- `description`: short description.
- `restart`: `always`, `on-failure`, or `never`.
- `health`: `ignore`, `restart`, or `stop`.
- `depends`: IDs of other ARC services in this registry. Forge resolves these
  recursively and installs missing dependencies in dependency-first order.
- `system_dependencies`: operating-system requirements checked by ARC. Each
  item uses `kind` (`shared-library` or `command`), `name`, optional `required`
  (defaults to `true`), and optional `description`.

Only repositories owned by `ARC-V2-AI` over HTTPS are accepted. `ref` selects
the branch or tag to clone; Forge records the resolved commit SHA in
`~/arc/arc.lock`. The service source remains in its own repository; this
repository only provides discovery and metadata.

## Registry maintenance

Keep the JSON valid and consistent with the referenced service repository's
`pyproject.toml`. Validate changes against `schemas/registry.schema.json` and
`schemas/service.schema.json` before merging. Use pull requests for catalog
changes and review repository ownership, service IDs, dependency edges, and
system dependency declarations before publishing.
