# valk-protobuf

Pure-[Valk](https://valk-lang.dev) Protocol Buffers (proto3): wire-format codec, `.proto` schema
parser, schema-driven dynamic messages, and a Valk code generator (`valk-protoc`). No C/C++ or
`protoc` dependencies.

Requires Valk 0.9.1 or newer.

## Install

```
vman install github.com/zikue/valk-protobuf
```

## Features

- **Wire format codec** (`Writer`, `Reader`, `WireType`, `ProtoMessage`):
  - Base-128 varints up to 64 bits with overflow and truncation checks (including 10-byte two's complement negative `int32`/`int64`).
  - ZigZag encoding (`sint32`, `sint64`), little-endian fixed integers (`fixed32`, `sfixed32`, `fixed64`, `sfixed64`), IEEE-754 `float`/`double`, and length-delimited `string`/`bytes`/submessages.
  - Packed and unpacked repeated scalar fields, bounds/depth-checked submessages (`MAX_DEPTH = 100`), and forward-compatible unknown field skipping (`skip_field`).
- **`.proto` schema parser** (`parse_proto`):
  - Full proto3 lexer and recursive-descent parser: `syntax`, `package`, `import` (`public`/`weak`), options (including custom parenthesized and aggregate options), nested `message` and `enum` (negative values, `allow_alias`), `oneof`, `map<K, V>`, `repeated`, `optional`, `reserved` ranges and names, `service`/`rpc` (with `stream`), and leading doc comment preservation.
- **Dynamic messages** (`DynamicMessage`):
  - Encode and decode protobuf binaries directly against a parsed `MessageDescriptor` without code generation.
- **Valk code generator** (`generate_valk` + `valk-protoc` CLI):
  - Emits clean, lint-passing (`valk build --lint --deny-warnings`) Valk `+ enum` and `+ class` definitions implementing `protobuf.ProtoMessage` with proto3 default omission, `optional` presence, packed encoding, packed & unpacked decoding, submessage merging, `oneof` exclusivity, maps, and unknown field skipping.

## Usage

### Wire Codec

```rust
use protobuf

fn main() {
    let w = protobuf.Writer.new()
    w.write_int32(1, 42)
    w.write_sint32(2, -100)
    w.write_string(3, "hello")
    w.write_packed_uint32(4, Array[u32]{ 1, 2, 3 })

    let bytes = w.into_bytes()
    let r = protobuf.Reader.new(bytes)
    while true {
        let tag = (r.next_tag() ! panic(E.message)) ?! break
        let field, wt = tag
        match field {
            1 => println(r.read_int32() ! panic(E.message))
            2 => println(r.read_sint32() ! panic(E.message))
            3 => println(r.read_string() ! panic(E.message))
            4 => println(r.read_packed_uint32() ! panic(E.message))
            default => r.skip_field(wt) ! panic(E.message)
        }
    }
}
```

### Schema Parser & Dynamic Message

```rust
use protobuf

let schema = "
syntax = \"proto3\";
package demo;

message User {
    int64 id = 1;
    string name = 2;
    repeated int32 scores = 3;
}
"

let file = protobuf.parse_proto(schema) ! panic("%{E.line}:%{E.column}: %{E.message}")
let desc = file.find_message("User") ?! panic("missing User")

let msg = protobuf.DynamicMessage.new(desc, file)
msg.set_int64("id", 7)
msg.set_string("name", "Alice")
let wire = msg.encode()

let decoded = protobuf.DynamicMessage.decode(desc, wire, file) ! panic(E.message)
println(decoded.get_string("name"))
```

### Valk Code Generator & CLI

Generate Valk code programmatically:

```rust
let file = protobuf.parse_proto(proto_source) ! panic(E.message)
let valk_source = protobuf.generate_valk(file)
```

Or build and run the `valk-protoc` CLI:

```sh
make cli
./valk-protoc schema.proto -o generated.valk
./valk-protoc schema.proto --grpc -o generated.valk   # also emit valk-grpc service/client stubs
```

Generated classes expose `encode_proto(w: protobuf.Writer)`, `to_proto_bytes() String`,
`decode_proto(data: local &[u8]) SELF !protobuf.Error`, `decode_proto_reader`, and
`merge_proto_reader`, and implement `protobuf.ProtoMessage`. When `--grpc` (or
`CodegenOptions { emit_grpc: true }`) is enabled, `service` blocks also generate
`<Service>Service`, `register_<Service>_service`, and `<Service>Client` targeting `valk-grpc`.

## API Documentation

See [docs/api.md](docs/api.md) for the concise API reference and [docs/api-full.md](docs/api-full.md) for the full documentation.

## Development

- `make lint` checks the sources with `--deny-warnings`
- `make test` runs the full test suite (including end-to-end generated-code compilation and execution)
- `make example` runs the runnable example
- `make cli` builds the `valk-protoc` CLI tool
- `make docs` regenerates `docs/api.md` and `docs/api-full.md`
