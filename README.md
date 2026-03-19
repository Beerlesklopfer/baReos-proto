# baReos-proto

Protocol Buffer definitions for the Bareos Director gRPC interface.

Shared between the C++ Bareos Director and the Rust baReos Director (`baReos-dird`).

## Files

| File | Description |
|------|-------------|
| `director_service.proto` | Director gRPC service (ExecuteCommand, ListCommands, InteractiveSession) |
| `director_commands.proto` | 88 command definitions with parameter schemas |
| `bareos_options.proto` | Custom protobuf options for command metadata |
| `plugin_ccc.proto` | Cross-cutting plugin interfaces (shared by FD/SD/DIR) |

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
```

### In C++ (CMake)

```cmake
BareosProtobuf(director_service.proto)
```
