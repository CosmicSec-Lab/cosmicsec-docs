# CLI Parsers#

**14 Security Parsers** — Nmap, Nuclei, Nikto, etc.

## Overview#

CosmicSec CLI includes 14 security tool parsers for processing scan output.

## Supported Parsers#

- **Nmap** — parse Nmap XML output#
- **Nuclei** — parse Nuclei JSON output#
- **Nikto** — parse Nikto text output#
- **Gobuster** — parse Gobuster directory output#
- **ffuf** — parse ffuf fuzzer output#
- **Hydra** — parse Hydra brute-force output#
- **Hashcat** — parse Hashcat cracking output#
- **John** — parse John the Ripper output#
- **Masscan** — parse Masscan output#
- **Metasploit** — parse Metasploit results#
- **SQLMap** — parse SQLMap output#
- **WPScan** — parse WPScan output#
- **ZAP** — parse OWASP ZAP output#

## Usage#

```bash#
# Parse Nmap output#
cosmicsec parse nmap --file scan.xml#

# Parse Nuclei results#
cosmicsec parse nuclei --file results.json#

# Parse multiple files#
cosmicsec parse nmap --file "scans/*.xml"#
```

## Custom Parsers#

```bash#
# Register custom parser#
cosmicsec parser register --name my-parser --script parser.py#
```

## Rust Accelerators#

High-performance parsers written in Rust:
- `rust_parsers` — faster XML/JSON parsing#
- Available for Nmap, Nuclei, Nikto#
