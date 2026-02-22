# Market Data Feed Handler

**Codename:** `conduit`

## Purpose

Build a market data feed handler that parses Nasdaq's ITCH 5.0 binary protocol,
reconstructs the full order book from the message stream, and exposes a
normalized, queryable view of market state.

When complete, `conduit` feeds directly into
[vortex](https://github.com/dimasusername/vortex), forming an end-to-end market
data → order book pipeline.

## Constraints

- **Language**: C++17 minimum, C++20 permitted.
- **Dependencies**: Zero runtime dependencies for the core parser and book
builder. GoogleTest/Catch2 for testing, Google Benchmark for benchmarks. `mmap`
for file I/O.
- **Build system**: CMake. Must build cleanly with
`-Wall -Wextra -Wpedantic -Werror`.
- **Platform**: Linux x86_64.

## Background: ITCH 5.0

Nasdaq TotalView-ITCH 5.0 is a binary protocol that communicates the full depth
of book for all Nasdaq-listed securities. The spec is publicly available from
Nasdaq:

**Spec URL**: <https://www.nasdaqtrader.com/content/technicalsupport/specifications/dataproducts/NQTVITCHspecification.pdf>

**Sample data**: Nasdaq provides historical ITCH data files. A single day's file
is typically 5-8 GB uncompressed.

The protocol is a stream of variable-length binary messages. Each message begins
with a 2-byte big-endian length field (in the file format), followed by a 1-byte
message type, followed by type-specific fields. All multi-byte integers are
big-endian. All prices are in fixed-point format (price × 10,000 stored as a
32-bit integer, except for some 64-bit fields).
