# baReos-proto

Protocol Buffer definitions for the Bareos Director gRPC interface.

Shared between the C++ Bareos Director and the Rust baReos Director (`baReos-dird`).

## Files

| File | Description |
|------|-------------|
| `director_service.proto` | Director gRPC service (GetServerInfo, Login, ExecuteCommand, ListCommands, InteractiveSession) |
| `director_api.proto` | Typed Director API (`bareos.director.api.DirectorApi`): one RPC per command/subcommand with structured results |
| `director_commands.proto` | 88 command definitions with parameter schemas |
| `bareos_options.proto` | Custom protobuf options for command metadata |
| `plugin_ccc.proto` | Cross-cutting plugin interfaces (shared by FD/SD/DIR) |

## Typed Director API (`director_api.proto`)

`DirectorApi` runs on the same gRPC port as `Director` and uses the same
login (session token in the metadata header `x-onesimus-session`) and the
same console ACLs. Requests are the `*Command` messages of
`director_commands.proto` where they carry all parameters of a command form,
otherwise new `*Command` messages in `director_api.proto` (with the
`bareos_options.proto` options). Responses are `*Response` messages built
from `*Info` records whose field names are the keys of the Director's
API 2 JSON output (`.api 2`); values without a typed field are kept in
`extra_fields`.

- Read RPCs (list/llist, status, show, dot listings, BVFS/OVFS queries) are
  implemented by onesimus-dird.
- Write, session and restore-tree RPCs are defined but answer
  `UNIMPLEMENTED` until the Director implements them (S-35).
- `JobStatus`, `JobType` and `JobLevel` enum numbers are the ASCII codes of
  the catalog characters (`JOB_STATUS_TERMINATED = 84 = 'T'`).
- Imports `google/protobuf/struct.proto` (well-known types, e.g. Debian
  package `libprotobuf-dev`).

Documentation (German): `docs/wiki/grpc-api.md` in baReos-dird.

## Usage

### As Git submodule

```bash
git submodule add git@github.com:Beerlesklopfer/baReos-proto.git proto
```

### In Rust (tonic-build)

```rust
// build.rs
tonic_build::configure()
    .build_server(true)
    .build_client(true)
    .compile_protos(
        &["proto/director_service.proto"],
        &["proto/"],
    )
    .unwrap();

// typed API: servers that implement only part of it can let the generated
// default methods answer UNIMPLEMENTED
tonic_build::configure()
    .build_server(true)
    .build_client(true)
    .generate_default_stubs(true)
    .compile_protos(&["proto/director_api.proto"], &["proto/"])
    .unwrap();
```

### In C++ (CMake)

```cmake
BareosProtobuf(director_service.proto)
BareosProtobuf(director_api.proto)
```

## License

GNU Affero General Public License v3.0 only (`AGPL-3.0-only`); see [LICENSE](LICENSE).
Existing Bareos copyright notices are preserved.

Maintainer: Joerg Bernau <Joerg.Bernau@onesimus.io>.
