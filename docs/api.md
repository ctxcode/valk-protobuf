
# Documentation

Namespaces: [main](#main)

---

# main

## Aliases for 'main'

```js
// Maximum nested submessage depth allowed when decoding.
+ value MAX_DEPTH (100)
// Maximum valid protobuf field number (`2^29 - 1`).
+ value MAX_FIELD_NUMBER (536870911)
```

## Enums for 'main'

```js
// Discriminant for a dynamically typed protobuf field value.
+ enum DynamicKind { none, int32, int64, uint32, uint64, float32, float64, bool_val, string_val, bytes_val, enum_val, message_val, list_val, map_val }
// Field cardinality / presence label in a `.proto` message.
+ enum FieldLabel { none, optional, repeated }
// Protocol Buffers wire types (proto3).
+ enum WireType : u8 { varint (0), fixed64 (1), len (2), fixed32 (5) }
```

## Functions for 'main'

```js
// Reinterprets a raw 32-bit unsigned bit pattern as an IEEE-754 `f32`.
+ fn f32_from_bits(bits: u32) f32
// Reinterprets an IEEE-754 `f32` value as its raw 32-bit unsigned bit pattern.
+ fn f32_to_bits(v: f32) u32
// Reinterprets a raw 64-bit unsigned bit pattern as an IEEE-754 `f64`.
+ fn f64_from_bits(bits: u64) f64
// Reinterprets an IEEE-754 `f64` value as its raw 64-bit unsigned bit pattern.
+ fn f64_to_bits(v: f64) u64
// Generates idiomatic Valk 0.8.10 source code from a parsed `FileDescriptor`.
+ fn generate_valk(file: FileDescriptor, options: ?CodegenOptions (null)) String
// Returns true if `type_name` is a scalar type that supports packed repeated encoding.
+ fn is_packable_scalar(type_name: String) bool
// Returns true if `type_name` is a built-in Protocol Buffers scalar type.
+ fn is_scalar_type(type_name: String) bool
// Returns true if `type_name` is a valid protobuf `map` key type.
+ fn is_valid_map_key_type(type_name: String) bool
// Encodes a field number and wire type into a protobuf tag varint value.
+ fn make_tag(field_number: u32, wire_type: WireType) u32
// Parses a `.proto` (proto3) schema source string into a `FileDescriptor` AST.
+ fn parse_proto(source: String) FileDescriptor !Error
// Returns the wire type for a built-in scalar `type_name`, or `null` if non-scalar.
+ fn scalar_wire_type(type_name: String) ?WireType
// Parses a raw 3-bit wire type value into `WireType`.
+ fn wire_type_from_u8(v: u8, offset: uint (0)) WireType !Error
// ZigZag-decodes an unsigned 32-bit integer into a signed 32-bit integer (`sint32`).
+ fn zigzag_decode32(v: u32) i32
// ZigZag-decodes an unsigned 64-bit integer into a signed 64-bit integer (`sint64`).
+ fn zigzag_decode64(v: u64) i64
// ZigZag-encodes a signed 32-bit integer (`sint32`) into an unsigned 32-bit integer.
+ fn zigzag_encode32(v: i32) u32
// ZigZag-encodes a signed 64-bit integer (`sint64`) into an unsigned 64-bit integer.
+ fn zigzag_encode64(v: i64) u64
```

## Classes for 'main'

```js
// Configuration options for Valk code generation from a parsed `.proto` file.
+ class CodegenOptions {
    // Emit the standard generated-file header comment at the top of output.
    + emit_header: bool
    // Module name imported for the protobuf runtime (`use <protobuf_import>`).
    + protobuf_import: String
    // Emit `+` public visibility modifiers on generated types, fields, and methods.
    + public_types: bool
}
```

```js
// Key/value entry inside a dynamic protobuf `map<K, V>` field.
+ class DynamicMapEntry {
    // Decoded map key.
    + key: DynamicValue
    // Decoded map value.
    + value: DynamicValue
}
```

```js
// Schema-driven dynamic Protocol Buffers message that encodes and decodes without codegen.
+ class DynamicMessage is ProtoMessage {
    // Schema descriptor for this message.
    + descriptor: MessageDescriptor
    // Populated field values keyed by field name.
    + fields: Map[DynamicValue]
    // Optional enclosing `FileDescriptor` used to resolve sibling message and enum types.
    + file: ?FileDescriptor

    // Removes field `name` from this message.
    + fn clear(name: String) void
    // Decodes protobuf wire bytes into a `DynamicMessage` described by `descriptor`.
    + static fn decode(descriptor: MessageDescriptor, data: local &[u8], file: ?FileDescriptor (null)) DynamicMessage !Error
    // Decodes a `DynamicMessage` from an existing `Reader` (preserving depth limits).
    + static fn decode_reader(descriptor: MessageDescriptor, r: Reader, file: ?FileDescriptor (null)) DynamicMessage !Error
    // Encodes this dynamic message into protobuf wire bytes.
    + fn encode() String
    // Implements `ProtoMessage.encode_proto`.
    + fn encode_proto(w: Writer) void
    // Returns the `DynamicValue` for field `name`, or `null` if unset.
    + fn get(name: String) ?DynamicValue
    // Gets a `bool` field or `false` if unset.
    + fn get_bool(name: String) bool
    // Gets a `double` field or `0.0` if unset.
    + fn get_double(name: String) f64
    // Gets an `enum` numeric value or `0` if unset.
    + fn get_enum(name: String) i32
    // Gets a `float` field or `0.0` if unset.
    + fn get_float(name: String) f32
    // Gets an `int32` field or `0` if unset.
    + fn get_int32(name: String) i32
    // Gets an `int64` field or `0` if unset.
    + fn get_int64(name: String) i64
    // Gets a repeated list field (returns an empty array if unset).
    + fn get_list(name: String) Array[DynamicValue]
    // Gets a map field's entries (returns an empty array if unset).
    + fn get_map(name: String) Array[DynamicMapEntry]
    // Gets a submessage field or `null` if unset.
    + fn get_message(name: String) ?DynamicMessage
    // Gets a `string` or `bytes` field or `""` if unset.
    + fn get_string(name: String) String
    // Gets a `uint32` field or `0` if unset.
    + fn get_uint32(name: String) u32
    // Gets a `uint64` field or `0` if unset.
    + fn get_uint64(name: String) u64
    // Returns true if a field named `name` has been set on this message.
    + fn has(name: String) bool
    // Creates an empty dynamic message for `descriptor`.
    + static fn new(descriptor: MessageDescriptor, file: ?FileDescriptor (null)) DynamicMessage
    // Sets field `name` to `value`, clearing sibling fields if `name` belongs to a `oneof`.
    + fn set(name: String, value: DynamicValue) void
    // Sets a `bool` field.
    + fn set_bool(name: String, v: bool) void
    // Sets a `bytes` field.
    + fn set_bytes(name: String, v: String) void
    // Sets a `double` field.
    + fn set_double(name: String, v: f64) void
    // Sets an `enum` field by numeric value.
    + fn set_enum(name: String, v: i32) void
    // Sets a `float` field.
    + fn set_float(name: String, v: f32) void
    // Sets an `int32` field.
    + fn set_int32(name: String, v: i32) void
    // Sets an `int64` field.
    + fn set_int64(name: String, v: i64) void
    // Sets a repeated list field.
    + fn set_list(name: String, v: Array[DynamicValue]) void
    // Inserts or updates a key/value pair on a `map<K, V>` field.
    + fn set_map_entry(name: String, key: DynamicValue, value: DynamicValue) void
    // Sets a submessage field.
    + fn set_message(name: String, v: DynamicMessage) void
    // Sets a `string` field.
    + fn set_string(name: String, v: String) void
    // Sets a `uint32` field.
    + fn set_uint32(name: String, v: u32) void
    // Sets a `uint64` field.
    + fn set_uint64(name: String, v: u64) void
    // Implements `ProtoMessage.to_proto_bytes`.
    + fn to_proto_bytes() String
}
```

```js
// Dynamically typed value stored in a `DynamicMessage`.
+ class DynamicValue {
    + bool_value: bool
    + double_value: f64
    + float_value: f32
    + int32_value: i32
    + int64_value: i64
    // Active value kind.
    + kind: DynamicKind
    + list_value: Array[DynamicValue]
    + map_value: Array[DynamicMapEntry]
    + message_value: ?DynamicMessage
    + string_value: String
    + uint32_value: u32
    + uint64_value: u64

    // Creates a `bool` dynamic value.
    + static fn from_bool(v: bool) DynamicValue
    // Creates a `bytes` dynamic value.
    + static fn from_bytes(v: String) DynamicValue
    // Creates a `double` dynamic value.
    + static fn from_double(v: f64) DynamicValue
    // Creates an `enum` dynamic value from its numeric wire value.
    + static fn from_enum(v: i32) DynamicValue
    // Creates a `float` dynamic value.
    + static fn from_float(v: f32) DynamicValue
    // Creates an `int32` dynamic value.
    + static fn from_int32(v: i32) DynamicValue
    // Creates an `int64` dynamic value.
    + static fn from_int64(v: i64) DynamicValue
    // Creates a repeated-list dynamic value.
    + static fn from_list(items: Array[DynamicValue]) DynamicValue
    // Creates a map dynamic value.
    + static fn from_map(entries: Array[DynamicMapEntry]) DynamicValue
    // Creates a submessage dynamic value.
    + static fn from_message(v: DynamicMessage) DynamicValue
    // Creates a `string` dynamic value.
    + static fn from_string(v: String) DynamicValue
    // Creates a `uint32` dynamic value.
    + static fn from_uint32(v: u32) DynamicValue
    // Creates a `uint64` dynamic value.
    + static fn from_uint64(v: u64) DynamicValue
    // Returns true if two scalar keys are equal (used for map key deduplication).
    + fn key_equals(other: DynamicValue) bool
    // Looks up a string-keyed entry in a `map_val`, or returns `null`.
    + fn map_get_string(key: String) ?DynamicValue
}
```

```js
// A parsed `.proto` `enum` definition.
+ class EnumDescriptor {
    // True when `option allow_alias = true;` was declared.
    + allow_alias: bool
    // Leading documentation comment, if any.
    + doc: String
    // Unqualified enum name.
    + name: String
    // Enum-level options.
    + options: Array[ProtoOption]
    // Reserved value names.
    + reserved_names: Array[String]
    // Reserved numeric ranges.
    + reserved_ranges: Array[ReservedRange]
    // Enum values in declaration order.
    + values: Array[EnumValueDescriptor]

    // Finds an enum value by `name`, or returns `null`.
    + fn value_by_name(name: String) ?EnumValueDescriptor
    // Finds the first enum value matching `number`, or returns `null`.
    + fn value_by_number(number: i32) ?EnumValueDescriptor
}
```

```js
// A single value inside a `.proto` `enum`.
+ class EnumValueDescriptor {
    // Leading documentation comment, if any.
    + doc: String
    // Enum value identifier (e.g., `STATUS_OK`).
    + name: String
    // Signed 32-bit wire number.
    + number: i32
    // Inline value options (e.g., `[deprecated = true]`).
    + options: Array[ProtoOption]
}
```

```js
// Thrown when encoding, decoding, or parsing Protocol Buffers data fails.
+ union Error {
    truncated(String, uint)
    varint_overflow(String, uint)
    invalid_wire_type(String, uint)
    invalid_field_number(String, uint, uint, uint)
    wire_type_mismatch(String, uint)
    depth_limit(String, uint)
    length_overflow(String, uint)
    syntax_error(String, uint, uint, uint)
    unknown_type(String, uint)
    invalid_value(String, uint)

    // Returns the 1-based column number for `.proto` parse errors, or `0` for wire errors.
    + get column: uint
    // Returns the 1-based line number for `.proto` parse errors, or `0` for wire errors.
    + get line: uint
    // Returns the error description carried by any variant.
    + get message: String
    // Returns the byte offset associated with the error, or `0` if unavailable.
    + get offset: uint
    + fn to_string() String
}
```

```js
// A parsed field inside a `.proto` `message` or `oneof`.
+ class FieldDescriptor {
    // Leading documentation comment, if any.
    + doc: String
    // True for `map<K, V>` fields.
    + is_map: bool
    // Field label (`none`, `optional`, or `repeated`).
    + label: FieldLabel
    // Map key scalar type when `is_map` is true.
    + map_key_type: String
    // Map value type when `is_map` is true.
    + map_value_type: String
    // Field name.
    + name: String
    // Field tag number (`1 ..= 536870911`).
    + number: u32
    // Enclosing `oneof` name, or empty string if not part of a `oneof`.
    + oneof_name: String
    // Field-level options (e.g., `[packed = true]`, `[deprecated = true]`).
    + options: Array[ProtoOption]
    // Protobuf type name (scalar, enum, message, or `"map<K, V>"`).
    + type_name: String

    // Looks up a field option by name, or returns `null`.
    + fn get_option(name: String) ?String
    // Returns true if this field has `[deprecated = true]`.
    + get is_deprecated: bool
    // Returns true if this field belongs to a `oneof`.
    + get is_oneof: bool
    // Returns true if this field is marked `optional`.
    + get is_optional: bool
    // Returns true if this repeated field should be encoded packed (proto3 default for packable scalars unless `[packed = false]`).
    + get is_packed: bool
    // Returns true if this field is `repeated` (and not a map).
    + get is_repeated: bool
}
```

```js
// Root AST representing a parsed `.proto` file.
+ class FileDescriptor {
    // Leading file-level documentation comment, if any.
    + doc: String
    // Top-level `enum` definitions.
    + enums: Array[EnumDescriptor]
    // `import` statements.
    + imports: Array[ImportDescriptor]
    // Top-level `message` definitions.
    + messages: Array[MessageDescriptor]
    // File-level `option` declarations.
    + options: Array[ProtoOption]
    // Declared `package` name, or empty string if omitted.
    + package_name: String
    // Top-level `service` definitions.
    + services: Array[ServiceDescriptor]
    // Declared syntax version (e.g., `"proto3"`).
    + syntax: String

    // Finds an enum by unqualified, dotted nested, or package-qualified name.
    + fn find_enum(name: String) ?EnumDescriptor
    // Finds a message by unqualified, dotted nested, or package-qualified name.
    + fn find_message(name: String) ?MessageDescriptor
    // Looks up a file-level option by name, or returns `null`.
    + fn get_option(name: String) ?String
}
```

```js
// A `.proto` `import` declaration.
+ class ImportDescriptor {
    // True for `import public "..."`.
    + is_public: bool
    // True for `import weak "..."`.
    + is_weak: bool
    // Imported `.proto` path.
    + path: String
}
```

```js
// A parsed `.proto` `message` definition.
+ class MessageDescriptor {
    // Leading documentation comment, if any.
    + doc: String
    // All fields declared in this message (including `oneof` member fields).
    + fields: Array[FieldDescriptor]
    // Unqualified message name.
    + name: String
    // Nested `enum` definitions.
    + nested_enums: Array[EnumDescriptor]
    // Nested `message` definitions.
    + nested_messages: Array[MessageDescriptor]
    // `oneof` groups declared in this message.
    + oneofs: Array[OneofDescriptor]
    // Message-level options.
    + options: Array[ProtoOption]
    // Reserved field names.
    + reserved_names: Array[String]
    // Reserved field number ranges.
    + reserved_ranges: Array[ReservedRange]

    // Finds a field by `name`, or returns `null`.
    + fn field_by_name(name: String) ?FieldDescriptor
    // Finds a field by tag `number`, or returns `null`.
    + fn field_by_number(number: u32) ?FieldDescriptor
    // Finds a nested enum by direct or dotted relative `name`, or returns `null`.
    + fn find_enum(name: String) ?EnumDescriptor
    // Finds a nested message by direct or dotted relative `name`, or returns `null`.
    + fn find_message(name: String) ?MessageDescriptor
    // Looks up a message option by name, or returns `null`.
    + fn get_option(name: String) ?String
}
```

```js
// A parsed `oneof` group inside a `.proto` `message`.
+ class OneofDescriptor {
    // Leading documentation comment, if any.
    + doc: String
    // Member fields belonging to this `oneof`.
    + fields: Array[FieldDescriptor]
    // Oneof group name.
    + name: String
    // Oneof-level options.
    + options: Array[ProtoOption]
}
```

```js
// Interface implemented by generated and hand-written Protocol Buffers messages.
+ interface ProtoMessage {
    // Writes this message's fields into `w`.
    + fn encode_proto(w: Writer) void
    // Encodes this message into a wire-format byte string.
    + fn to_proto_bytes() String
}
```

```js
// A `.proto` option entry (`name = value`).
+ class ProtoOption {
    // Option name (may include parenthesized custom option segments).
    + name: String
    // Raw or unquoted option value representation.
    + value: String
}
```

```js
// Bounds- and depth-checked Protocol Buffers wire-format reader.
+ class Reader {
    // Current submessage nesting depth.
    + depth: uint
    // Maximum allowed submessage nesting depth.
    + max_depth: uint
    // Current byte offset in `source`.
    + pos: uint
    // Source byte view.
    + source: &[u8]

    // Creates a new reader from any byte slice (including stack-local slices).
    + static fn from_bytes(data: local &[u8], max_depth: uint (MAX_DEPTH)) Reader
    // Returns true when all bytes in the current reader slice have been consumed.
    + get is_eof: bool
    // Creates a new zero-copy reader over heap-backed `data`.
    + static fn new(data: &[u8], max_depth: uint (MAX_DEPTH)) Reader
    // Reads the next `(field_number, wire_type)` tag, or returns `null` at EOF.
    + fn next_tag() ?(u32, WireType) !Error
    // Reads a protobuf `bool` varint.
    + fn read_bool() bool !Error
    // Reads a length-delimited byte slice as a `String`.
    + fn read_bytes() String !Error
    // Reads an IEEE-754 64-bit little-endian `double`.
    + fn read_double() f64 !Error
    // Reads a protobuf `enum` value as a 32-bit signed integer.
    + fn read_enum() i32 !Error
    // Reads a 32-bit little-endian `fixed32` integer.
    + fn read_fixed32() u32 !Error
    // Reads a 64-bit little-endian `fixed64` integer.
    + fn read_fixed64() u64 !Error
    // Reads an IEEE-754 32-bit little-endian `float`.
    + fn read_float() f32 !Error
    // Reads a protobuf `int32` varint.
    + fn read_int32() i32 !Error
    // Reads a protobuf `int64` varint.
    + fn read_int64() i64 !Error
    // Reads a packed repeated `bool` field.
    + fn read_packed_bool() Array[bool] !Error
    // Appends a packed repeated `bool` field into `out`.
    + fn read_packed_bool_into(out: Array[bool]) void !Error
    // Reads a packed repeated `double` field.
    + fn read_packed_double() Array[f64] !Error
    // Appends a packed repeated `double` field into `out`.
    + fn read_packed_double_into(out: Array[f64]) void !Error
    // Reads a packed repeated `enum` field.
    + fn read_packed_enum() Array[i32] !Error
    // Appends a packed repeated `enum` field into `out`.
    + fn read_packed_enum_into(out: Array[i32]) void !Error
    // Reads a packed repeated `fixed32` field.
    + fn read_packed_fixed32() Array[u32] !Error
    // Appends a packed repeated `fixed32` field into `out`.
    + fn read_packed_fixed32_into(out: Array[u32]) void !Error
    // Reads a packed repeated `fixed64` field.
    + fn read_packed_fixed64() Array[u64] !Error
    // Appends a packed repeated `fixed64` field into `out`.
    + fn read_packed_fixed64_into(out: Array[u64]) void !Error
    // Reads a packed repeated `float` field.
    + fn read_packed_float() Array[f32] !Error
    // Appends a packed repeated `float` field into `out`.
    + fn read_packed_float_into(out: Array[f32]) void !Error
    // Reads a packed repeated `int32` field.
    + fn read_packed_int32() Array[i32] !Error
    // Appends a packed repeated `int32` field into `out`.
    + fn read_packed_int32_into(out: Array[i32]) void !Error
    // Reads a packed repeated `int64` field.
    + fn read_packed_int64() Array[i64] !Error
    // Appends a packed repeated `int64` field into `out`.
    + fn read_packed_int64_into(out: Array[i64]) void !Error
    // Reads a packed repeated `sfixed32` field.
    + fn read_packed_sfixed32() Array[i32] !Error
    // Appends a packed repeated `sfixed32` field into `out`.
    + fn read_packed_sfixed32_into(out: Array[i32]) void !Error
    // Reads a packed repeated `sfixed64` field.
    + fn read_packed_sfixed64() Array[i64] !Error
    // Appends a packed repeated `sfixed64` field into `out`.
    + fn read_packed_sfixed64_into(out: Array[i64]) void !Error
    // Reads a packed repeated `sint32` field.
    + fn read_packed_sint32() Array[i32] !Error
    // Appends a packed repeated `sint32` field into `out`.
    + fn read_packed_sint32_into(out: Array[i32]) void !Error
    // Reads a packed repeated `sint64` field.
    + fn read_packed_sint64() Array[i64] !Error
    // Appends a packed repeated `sint64` field into `out`.
    + fn read_packed_sint64_into(out: Array[i64]) void !Error
    // Reads a packed repeated `uint32` field.
    + fn read_packed_uint32() Array[u32] !Error
    // Appends a packed repeated `uint32` field into `out`.
    + fn read_packed_uint32_into(out: Array[u32]) void !Error
    // Reads a packed repeated `uint64` field.
    + fn read_packed_uint64() Array[u64] !Error
    // Appends a packed repeated `uint64` field into `out`.
    + fn read_packed_uint64_into(out: Array[u64]) void !Error
    // Reads a 32-bit little-endian `sfixed32` integer.
    + fn read_sfixed32() i32 !Error
    // Reads a 64-bit little-endian `sfixed64` integer.
    + fn read_sfixed64() i64 !Error
    // Reads a ZigZag-encoded `sint32` varint.
    + fn read_sint32() i32 !Error
    // Reads a ZigZag-encoded `sint64` varint.
    + fn read_sint64() i64 !Error
    // Reads a length-delimited UTF-8 `string` field.
    + fn read_string() String !Error
    // Reads a length-delimited submessage and returns a bounded sub-`Reader` with incremented depth.
    + fn read_submessage() Reader !Error
    // Reads a protobuf `uint32` varint.
    + fn read_uint32() u32 !Error
    // Reads a protobuf `uint64` varint.
    + fn read_uint64() u64 !Error
    // Reads a base-128 varint up to 64 bits.
    + fn read_varint() u64 !Error
    // Returns the number of unread bytes remaining.
    + get remaining: uint
    // Skips an unknown field's value according to its `wire_type`.
    + fn skip_field(wire_type: WireType) void !Error
}
```

```js
// An inclusive reserved field-number range (`start` ..= `end`).
+ class ReservedRange {
    // Last reserved number (inclusive).
    + end: i32
    // First reserved number (inclusive).
    + start: i32
}
```

```js
// A parsed `rpc` method inside a `.proto` `service`.
+ class RpcDescriptor {
    // True if the request type is marked `stream`.
    + client_streaming: bool
    // Leading documentation comment, if any.
    + doc: String
    // Request message type name.
    + input_type: String
    // Method name.
    + name: String
    // RPC-level options.
    + options: Array[ProtoOption]
    // Response message type name.
    + output_type: String
    // True if the response type is marked `stream`.
    + server_streaming: bool
}
```

```js
// A parsed `.proto` `service` definition.
+ class ServiceDescriptor {
    // Leading documentation comment, if any.
    + doc: String
    // RPC methods in declaration order.
    + methods: Array[RpcDescriptor]
    // Service name.
    + name: String
    // Service-level options.
    + options: Array[ProtoOption]
}
```

```js
// Streaming Protocol Buffers wire-format writer backed by a `ByteBuffer`.
+ class Writer {
    // Underlying output byte buffer.
    + buffer: ByteBuffer

    // Consumes the buffer and returns the written wire bytes as a `String`.
    + fn into_bytes() String
    // Returns the number of bytes written so far.
    + get length: uint
    // Creates a new writer with an initial buffer capacity.
    + static fn new(capacity: uint (128)) Writer
    // Clears all written bytes while keeping the allocated buffer capacity.
    + fn reset() void
    // Creates a writer that appends directly into an existing `ByteBuffer`.
    + static fn to_buffer(buffer: ByteBuffer) Writer
    // Returns a copy of the written wire bytes as a `String`.
    + fn to_bytes() String
    // Writes a tagged `bool` field.
    + fn write_bool(field: u32, v: bool) void
    // Writes a tagged `bytes` field.
    + fn write_bytes(field: u32, v: local &[u8]) void
    // Writes a tagged `double` field.
    + fn write_double(field: u32, v: f64) void
    // Writes a tagged `enum` field.
    + fn write_enum(field: u32, v: i32) void
    // Writes a tagged `fixed32` field.
    + fn write_fixed32(field: u32, v: u32) void
    // Writes a tagged `fixed64` field.
    + fn write_fixed64(field: u32, v: u64) void
    // Writes a tagged `float` field.
    + fn write_float(field: u32, v: f32) void
    // Writes a tagged `int32` field.
    + fn write_int32(field: u32, v: i32) void
    // Writes a tagged `int64` field.
    + fn write_int64(field: u32, v: i64) void
    // Writes an embedded message field implementing `ProtoMessage`.
    + fn write_message(field: u32, msg: ProtoMessage) void
    // Writes pre-encoded bytes as an embedded message field.
    + fn write_message_bytes(field: u32, data: local &[u8]) void
    // Writes a packed repeated `bool` field. Empty arrays emit nothing.
    + fn write_packed_bool(field: u32, values: Array[bool]) void
    // Writes a packed repeated `double` field. Empty arrays emit nothing.
    + fn write_packed_double(field: u32, values: Array[f64]) void
    // Writes a packed repeated `enum` field. Empty arrays emit nothing.
    + fn write_packed_enum(field: u32, values: Array[i32]) void
    // Writes a packed repeated `fixed32` field. Empty arrays emit nothing.
    + fn write_packed_fixed32(field: u32, values: Array[u32]) void
    // Writes a packed repeated `fixed64` field. Empty arrays emit nothing.
    + fn write_packed_fixed64(field: u32, values: Array[u64]) void
    // Writes a packed repeated `float` field. Empty arrays emit nothing.
    + fn write_packed_float(field: u32, values: Array[f32]) void
    // Writes a packed repeated `int32` field. Empty arrays emit nothing.
    + fn write_packed_int32(field: u32, values: Array[i32]) void
    // Writes a packed repeated `int64` field. Empty arrays emit nothing.
    + fn write_packed_int64(field: u32, values: Array[i64]) void
    // Writes a packed repeated `sfixed32` field. Empty arrays emit nothing.
    + fn write_packed_sfixed32(field: u32, values: Array[i32]) void
    // Writes a packed repeated `sfixed64` field. Empty arrays emit nothing.
    + fn write_packed_sfixed64(field: u32, values: Array[i64]) void
    // Writes a packed repeated `sint32` field. Empty arrays emit nothing.
    + fn write_packed_sint32(field: u32, values: Array[i32]) void
    // Writes a packed repeated `sint64` field. Empty arrays emit nothing.
    + fn write_packed_sint64(field: u32, values: Array[i64]) void
    // Writes a packed repeated `uint32` field. Empty arrays emit nothing.
    + fn write_packed_uint32(field: u32, values: Array[u32]) void
    // Writes a packed repeated `uint64` field. Empty arrays emit nothing.
    + fn write_packed_uint64(field: u32, values: Array[u64]) void
    // Writes a raw `bool` varint (`0` or `1`).
    + fn write_raw_bool(v: bool) void
    // Writes a length-prefixed byte slice (length varint followed by raw bytes).
    + fn write_raw_bytes(data: local &[u8]) void
    // Writes a raw little-endian IEEE-754 64-bit double.
    + fn write_raw_double(v: f64) void
    // Writes a raw `enum` value as a sign-extended varint.
    + fn write_raw_enum(v: i32) void
    // Writes a raw little-endian 32-bit unsigned integer.
    + fn write_raw_fixed32(v: u32) void
    // Writes a raw little-endian 64-bit unsigned integer.
    + fn write_raw_fixed64(v: u64) void
    // Writes a raw little-endian IEEE-754 32-bit float.
    + fn write_raw_float(v: f32) void
    // Writes a raw `int32` varint (sign-extended to 64 bits for negative values per protobuf spec).
    + fn write_raw_int32(v: i32) void
    // Writes a raw `int64` varint.
    + fn write_raw_int64(v: i64) void
    // Writes a raw little-endian 32-bit signed integer.
    + fn write_raw_sfixed32(v: i32) void
    // Writes a raw little-endian 64-bit signed integer.
    + fn write_raw_sfixed64(v: i64) void
    // Writes a raw ZigZag-encoded `sint32` varint.
    + fn write_raw_sint32(v: i32) void
    // Writes a raw ZigZag-encoded `sint64` varint.
    + fn write_raw_sint64(v: i64) void
    // Writes a raw `uint32` varint.
    + fn write_raw_uint32(v: u32) void
    // Writes a raw `uint64` varint.
    + fn write_raw_uint64(v: u64) void
    // Writes a tagged `sfixed32` field.
    + fn write_sfixed32(field: u32, v: i32) void
    // Writes a tagged `sfixed64` field.
    + fn write_sfixed64(field: u32, v: i64) void
    // Writes a tagged ZigZag `sint32` field.
    + fn write_sint32(field: u32, v: i32) void
    // Writes a tagged ZigZag `sint64` field.
    + fn write_sint64(field: u32, v: i64) void
    // Writes a tagged `string` field.
    + fn write_string(field: u32, v: local &[u8]) void
    // Writes a field tag `(field << 3) | wire_type`.
    + fn write_tag(field: u32, wire_type: WireType) void
    // Writes a tagged `uint32` field.
    + fn write_uint32(field: u32, v: u32) void
    // Writes a tagged `uint64` field.
    + fn write_uint64(field: u32, v: u64) void
    // Writes a raw unsigned 64-bit base-128 varint (LEB128).
    + fn write_varint(v: u64) void
}
```
