# BHTTP/1 — Binary HTTP Protocol (v1)

A high-performance, compact request/response binary protocol carried over persistent TCP connections with 12-byte fixed-size framing, static header compression, and forward-compatible unknown frame type handling.

## 📌 Project Overview

**BHTTP/1** is a custom binary protocol designed as an alternative to ASCII-based HTTP/1.x, drawing design principles from HTTP/2 binary framing and HPACK header table indexing.

- **Track 1 — Server (`bserve`)**: Persistent TCP server capable of reading binary REQUEST frames, mapping paths to root directory files, and returning RESPONSE and DATA frames with 200, 400, and 404 handling.
- **Track 2 — Client (`bcurl`)**: Binary client supporting request construction, `-v` byte-level hex frame dumping, HTTP status validation, non-zero exit codes on 4xx/5xx errors, and connection reuse.

---

## 🚀 Key Features & Architectural Design

1. **Fixed 12-Byte Frame Header**:
   - `Length` (4B u32, payload size up to 16,384 bytes)
   - `Type` (1B u8: `0x01` REQUEST, `0x02` RESPONSE, `0x03` DATA, `0x04` ERROR)
   - `Flags` (1B u8: `0x01` `END_STREAM`)
   - `Reserved` (2B u16)
   - `Stream ID` (4B u32)

2. **Static Header Indexing**:
   - Static IDs for top headers (`1: content-type`, `2: content-length`, `3: server`, etc.)
   - ID `0` prefixing for custom headers.

3. **Version 2 Extensibility**:
   - Unknown frame types (`Type > 4`) are skipped cleanly by payload `Length` without closing the connection.

---

## 🛠 Quick Start & Usage

### Running the Server (`bserve`)
```bash
./bserve ./conformance/www 9000
```

### Running the Client (`bcurl`)
```bash
./bcurl -v localhost:9000/index.html
```

### Running Conformance & Interop Tests
```bash
python3 repo_bhttp/tests/interop/python_client/run_interop.py 127.0.0.1 9000 --www ./conformance/www
```

---

## 📚 Specification & Deliverables
- [`protocol/SPEC.md`](protocol/SPEC.md): Normative 2-page protocol specification.
- [`protocol/WIRE_FORMAT.md`](protocol/WIRE_FORMAT.md): Exact binary layout and wire format.
- [`protocol/HEADER_TABLE.md`](protocol/HEADER_TABLE.md): Static header table definitions.
- [`examples/hexdump/annotated-hexdump.md`](repo_bhttp/examples/hexdump/annotated-hexdump.md): Byte-by-byte annotated hexdump capture.
