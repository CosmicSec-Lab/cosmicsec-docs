# Rust Parsers#

**Rust-Accelerated Parsers** — high-performance parsing.

## Overview#

Rust-based parsers for faster XML/JSON parsing of security tool output.

## Features#

- **Faster parsing** — Rust is faster than Python for parsing#
- **Memory safe** — Rust's ownership model prevents memory issues#
- **Parallel processing** — use all CPU cores#
- **Python bindings** — seamless integration via PyO3#

## Available Parsers#

- **Nmap XML** — fast Nmap XML parsing#
- **Nuclei JSON** — fast Nuclei JSON parsing#
- **Nikto text** — parse Nikto output#
- **Gobuster** — directory enumeration results#

## Installation#

```bash#
# Install Rust parser support#
pip install cosmicsec-cli[rust]#
```

## Usage#

```bash#
# Use Rust parser for Nmap#
cosmicsec parse nmap --file scan.xml --use-rust#

# Use Rust parser for Nuclei#
cosmicsec parse nuclei --file results.json --use-rust#
```

## Building from Source#

```bash#
cd cosmicsec-cli/rust_parsers#
cargo build --release#
pip install -e .#
```

## Performance#

- **Nmap parsing**: 10x faster than Python#
- **Nuclei parsing**: 8x faster than Python#
- **Memory usage**: 50% less than Python#
