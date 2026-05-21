# hanzoai/proto

Schema definitions for the Hanzo platform. Mirrors `github.com/luxfi/proto`
in layout — `zap/` is the canonical default, `pb/` is the legacy protobuf
opt-in.

## Layout

```
proto/
├── zap/        ZAP-native schema (canonical default — no build tag)
│   └── <pkg>/  e.g. proto/zap/o11y/, proto/zap/trace/
└── pb/         protobuf schema (legacy — //go:build grpc)
    └── <pkg>/  e.g. proto/pb/o11y/, proto/pb/trace/
```

For any schema, both directories carry **the same package path** so importers
do `import "github.com/hanzoai/proto/zap/o11y"` and the Go build picks the
right file based on the build tag at the top of each file.

## Build tags

| Default (no tag) | `-tags grpc` |
|---|---|
| Compiles `zap/<pkg>/*_zap.go` (`//go:build !grpc`) | Compiles `pb/<pkg>/*_pb.go` (`//go:build grpc`) |
| ZAP-native types and codecs | Protobuf-generated types and codecs |
| Zero `google.golang.org/grpc` in dep graph | Includes `google.golang.org/grpc` |

ZAP is the canonical wire — the protobuf path exists only for external
integrations that mandate OTLP-over-gRPC. Internal Hanzo services use
ZAP exclusively.

## Conventions

- File suffix `_zap.go` and `_pb.go` to make the parallelism obvious.
- Same exported types in both — the build tag selects which definition wins.
- Conversion helpers live alongside (one direction only — ZAP → pb when an
  external bridge needs it; never the reverse, since pb is legacy-only).

See `~/work/lux/proto` and `~/work/lux/p2p/proto/{zap,pb}/` for the same pattern
on the Lux side.
