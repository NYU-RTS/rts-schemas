## Generation process

JSON schemas are generated from the OpenShift OpenAPI spec using `openapi2jsonschema` via `make`.

### Prerequisites

- `oc` CLI, logged into the target cluster
- `uv` and `jq`

### Build

```
make VERSION=v4.21
```

`VERSION` defaults to the version reported by `oc version`. Set it explicitly if `oc` is not connected to a cluster or the auto-detected value is wrong.

This produces four sets of schemas per version:

| Directory | Description |
|---|---|
| `v<X.Y>/` | Expanded refs, cross-references point to the published `_definitions.json` |
| `v<X.Y>-standalone/` | Self-contained (refs resolved inline) |
| `v<X.Y>-standalone-strict/` | Self-contained + `additionalProperties: false` |
| `v<X.Y>-local/` | Expanded refs, cross-references point to local files |

Each set also has a `v<X.Y>.0/` alias (e.g. `v4.21.0/`) copied from the base directory.

### Skipped definitions

Before generating schemas, definitions with fewer than 3 dot-separated name components are filtered out. `openapi2jsonschema --kubernetes` unconditionally parses `group` from index `-3` of the dot-split title and crashes on shorter names.

In practice these are all KubeVirt types that appear in the OpenShift spec under the bare `v1.<Kind>` naming convention (35 in v4.21), for example:

- `v1.VirtualMachineInstanceGuestAgentInfo`
- `v1.VirtualMachineInstanceFileSystemList`
- `v1.VirtualMachineMemoryDumpRequest`
- `v1.Disk`, `v1.DiskTarget`, `v1.CDRomTarget`
- `v1.MigrateOptions`, `v1.StartOptions`, `v1.StopOptions`

### Adding a new OpenShift version

Log in to a cluster running the target version, then:

```
make VERSION=v<X.Y>
```

If the raw spec doesn't exist yet, `make` will fetch it via `oc get --raw /openapi/v2` before generating schemas.

### Cleaning up

```
make clean        # remove generated schema directories
make mrproper     # also remove the cached OpenAPI spec
```
