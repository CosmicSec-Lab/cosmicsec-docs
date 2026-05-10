# 🛠️ CLI Parser Development Guide

## Overview

The **cosmicsec-cli** uses a powerful parser architecture that supports 14 security tools. Parsers are written in Python with optional **Rust accelerators** for 10x performance boost.

**Location:** `cosmicsec-cli/cli/parsers/`  
**Rust Accelerators:** `cosmicsec-cli/cli/agent/rust_parsers/`

---

## Parser Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                   CLI Input (XML/JSON/TXT)                 │
└──────────────────────────────┬──────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│              Parser Selection (by tool type)                 │
└──────────────────────────────┬──────────────────────────┘
                               │
              ┌────────────────┴────────────────┐
              │                                 │
              ▼                                 ▼
┌─────────────────────────┐      ┌─────────────────────────┐
│   Python Parser         │      │   Rust Accelerator       │
│   (Standard)           │      │   (Optional, 10x faster)│
│   - Easy to modify     │      │   - High performance     │
│   - Pure Python        │      │   - Memory safe          │
└─────────────────────────┘      └─────────────────────────┘
              │                                 │
              └────────────────┬────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│              Normalized Output (JSON)                        │
│  {                                                          │
│    "vulnerabilities": [...],                                │
│    "summary": {...}                                         │
│  }                                                          │
└─────────────────────────────────────────────────────────────┘
```

---

## Creating a New Parser

### Step 1: Create Parser File

```bash
cd cosmicsec-cli/cli/parsers
touch mytool_parser.py
```

### Step 2: Implement Parser Class

```python
# mytool_parser.py
import xml.etree.ElementTree as ET
import json
from typing import Dict, List, Any
from dataclasses import dataclass

@dataclass
class Vulnerability:
    vuln_id: str
    severity: str
    title: str
    description: str
    location: str
    cve: str = None
    cvss_score: float = None
    references: List[str] = None

class MyToolParser:
    """Parser for MyTool output (XML format)"""
    
    def __init__(self):
        self.vulnerabilities: List[Vulnerability] = []
        self.errors: List[str] = []
    
    def parse(self, file_path: str, target: str = None) -> Dict[str, Any]:
        """Parse MyTool output file and return normalized results"""
        try:
            tree = ET.parse(file_path)
            root = tree.getroot()
            
            # Parse vulnerabilities
            for issue in root.findall('.//issue'):
                vuln = self._parse_vulnerability(issue, target)
                if vuln:
                    self.vulnerabilities.append(vuln)
            
            return self._build_result(target)
        
        except ET.ParseError as e:
            self.errors.append(f"XML parse error: {str(e)}")
            return self._build_result(target)
    
    def _parse_vulnerability(self, element, target: str) -> Vulnerability:
        """Parse individual vulnerability from XML element"""
        try:
            severity_map = {
                '0': 'info',
                '1': 'low',
                '2': 'medium',
                '3': 'high',
                '4': 'critical'
            }
            
            return Vulnerability(
                vuln_id=f"vuln_{len(self.vulnerabilities) + 1}",
                severity=severity_map.get(
                    element.find('severity').text, 'medium'
                ),
                title=element.find('name').text,
                description=element.find('description').text,
                location=target or element.find('url').text,
                cve=element.find('cve').text if element.find('cve') is not None else None,
                cvss_score=float(element.find('cvss').text) if element.find('cvss') is not None else None,
                references=[ref.text for ref in element.findall('reference')]
            )
        except Exception as e:
            self.errors.append(f"Error parsing vulnerability: {str(e)}")
            return None
    
    def _build_result(self, target: str) -> Dict[str, Any]:
        """Build normalized result dictionary"""
        summary = {
            'critical': sum(1 for v in self.vulnerabilities if v.severity == 'critical'),
            'high': sum(1 for v in self.vulnerabilities if v.severity == 'high'),
            'medium': sum(1 for v in self.vulnerabilities if v.severity == 'medium'),
            'low': sum(1 for v in self.vulnerabilities if v.severity == 'low'),
            'info': sum(1 for v in self.vulnerabilities if v.severity == 'info')
        }
        
        return {
            'scan_id': f"parse_{hash(target)}",
            'target': target,
            'tool': 'mytool',
            'timestamp': datetime.now().isoformat(),
            'vulnerabilities': [vars(v) for v in self.vulnerabilities],
            'summary': summary,
            'errors': self.errors
        }
```

### Step 3: Register Parser

```python
# In cli/parsers/__init__.py
from .mytool_parser import MyToolParser

PARSERS = {
    'nmap': NmapParser,
    'nuclei': NucleiParser,
    'nikto': NiktoParser,
    'mytool': MyToolParser,  # Add your parser here
}

def get_parser(tool_name: str):
    parser_class = PARSERS.get(tool_name)
    if not parser_class:
        raise ValueError(f"No parser found for tool: {tool_name}")
    return parser_class()
```

### Step 4: Add CLI Command

```python
# In cli/commands/scan.py
@click.command()
@click.option('--tool', required=True, type=click.Choice(list(PARSERS.keys())))
@click.option('--file', required=True, type=click.Path(exists=True))
@click.option('--target', required=False)
@click.option('--rust/--no-rust', default=False, help='Use Rust accelerator')
def parse(tool, file, target, rust):
    """Parse tool output file"""
    
    if rust:
        # Use Rust accelerator if available
        try:
            from cosmicsec_cli.rust_accelerators import RustMyToolParser
            parser = RustMyToolParser()
            with open(file, 'r') as f:
                results = parser.parse(f.read())
        except ImportError:
            click.echo("Rust accelerators not installed. Using Python parser.")
            parser = get_parser(tool)
            results = parser.parse(file, target)
    else:
        parser = get_parser(tool)
        results = parser.parse(file, target)
    
    click.echo(json.dumps(results, indent=2))
```

---

## Adding Rust Accelerator (10x Faster)

### Step 1: Create Rust Parser

```rust
// cosmicsec-cli/cli/agent/rust_parsers/src/lib.rs
use pyo3::prelude::*;
use quick_xml::events::Event;
use quick_xml::reader::Reader;
use serde_json::{json, Value};
use std::collections::HashMap;

#[pyfunction]
fn parse_mytool(xml_content: &str) -> PyResult<String> {
    let mut reader = Reader::from_str(xml_content);
    let mut buf = Vec::new();
    let mut vulnerabilities = Vec::new();
    
    loop {
        match reader.read_event_into(&mut buf) {
            Ok(Event::Start(ref e)) if e.name().as_ref() == b"issue" => {
                let mut vuln = HashMap::new();
                // Parse XML elements...
                vulnerabilities.push(json!(vuln));
            }
            Ok(Event::Eof) => break,
            Err(e) => return Err(PyErr::new::<pyo3::exceptions::PyValueError, _>(
                format!("XML parse error: {}", e)
            )),
            _ => (),
        }
        buf.clear();
    }
    
    let result = json!({
        "tool": "mytool",
        "vulnerabilities": vulnerabilities,
        "summary": {
            "critical": 0,
            "high": 0,
            "medium": 0,
            "low": 0
        }
    });
    
    Ok(result.to_string())
}

#[pymodule]
fn cosmicsec_rust(_py: Python, m: &PyModule) -> PyResult<()> {
    m.add_function(wrap_pyfunction!(parse_mytool, m)?)?;
    Ok(())
}
```

### Step 2: Build Rust Library

```bash
cd cosmicsec-cli/cli/agent/rust_parsers
cargo build --release

# The output will be in target/release/
# For Python: libcosmicsec_rust.so (Linux) or cosmicsec_rust.pyd (Windows)
```

### Step 3: Python Wrapper for Rust

```python
# cli/rust_accelerators/__init__.py
import os
import sys

def get_rust_lib_path():
    """Find the compiled Rust library"""
    base_dir = os.path.dirname(__file__)
    if sys.platform == 'win32':
        lib_name = 'cosmicsec_rust.pyd'
    else:
        lib_name = 'libcosmicsec_rust.so'
    return os.path.join(base_dir, '..', 'rust_parsers', 'target', 'release', lib_name)

try:
    import cosmicsec_rust
    RUST_AVAILABLE = True
except ImportError:
    RUST_AVAILABLE = False

class RustMyToolParser:
    """Rust-accelerated MyTool parser"""
    
    def parse(self, xml_content: str):
        if not RUST_AVAILABLE:
            raise RuntimeError("Rust accelerators not available")
        
        return json.loads(cosmicsec_rust.parse_mytool(xml_content))
```

---

## Testing Your Parser

### Unit Tests

```python
# tests/test_mytool_parser.py
import pytest
from cosmicsec_cli.parsers.mytool_parser import MyToolParser

def test_parse_basic():
    parser = MyToolParser()
    result = parser.parse('tests/data/mytool_sample.xml', 'example.com')
    
    assert result['tool'] == 'mytool'
    assert len(result['vulnerabilities']) > 0
    assert result['summary']['critical'] >= 0

def test_parse_severity_mapping():
    parser = MyToolParser()
    result = parser.parse('tests/data/mytool_sample.xml', 'example.com')
    
    for vuln in result['vulnerabilities']:
        assert vuln['severity'] in ['critical', 'high', 'medium', 'low', 'info']

def test_rust_accelerator():
    try:
        from cosmicsec_cli.rust_accelerators import RustMyToolParser
        parser = RustMyToolParser()
        with open('tests/data/mytool_sample.xml', 'r') as f:
            result = parser.parse(f.read())
        assert 'vulnerabilities' in result
    except ImportError:
        pytest.skip("Rust accelerators not installed")
```

### Run Tests

```bash
# Run parser tests
pytest tests/test_mytool_parser.py -v

# Run with coverage
pytest tests/test_mytool_parser.py --cov=cosmicsec_cli.parsers.mytool_parser

# Test Rust accelerator
pytest tests/test_mytool_parser.py::test_rust_accelerator -v
```

---

## Supported Parser Formats

| Tool | Format | Parser Class | Rust Support |
|------|--------|--------------|--------------|
| **Nmap** | XML | `NmapParser` | ✅ |
| **Nuclei** | JSON | `NucleiParser` | ✅ |
| **Nikto** | TXT | `NiktoParser` | ✅ |
| **OWASP ZAP** | XML/JSON | `ZapParser` | ❌ |
| **Gobuster** | TXT | `GobusterParser` | ❌ |
| **Masscan** | TXT | `MasscanParser` | ❌ |
| **SSLyze** | JSON | `SSLyzeParser` | ❌ |
| **Snyk** | JSON | `SnykParser` | ❌ |
| **Trivy** | JSON | `TrivyParser` | ❌ |
| **Grype** | JSON | `GrypeParser` | ❌ |
| **Syft** | JSON | `SyftParser` | ❌ |
| **Semgrep** | JSON | `SemgrepParser` | ❌ |
| **Bandit** | JSON | `BanditParser` | ❌ |
| **Gitleaks** | JSON | `GitleaksParser` | ❌ |

---

## Advanced: Custom Normalization

### Custom Severity Mapping

```python
class MyToolParser:
    SEVERITY_MAP = {
        'critical': ['0', 'critical', '4'],
        'high': ['1', 'high', '3'],
        'medium': ['2', 'medium', '2'],
        'low': ['3', 'low', '1'],
        'info': ['4', 'info', '0']
    }
    
    def _normalize_severity(self, raw_severity: str) -> str:
        raw_lower = raw_severity.lower()
        for normalized, variants in self.SEVERITY_MAP.items():
            if raw_lower in [v.lower() for v in variants]:
                return normalized
        return 'medium'  # Default
```

### Extracting CVEs

```python
import re

class MyToolParser:
    CVE_PATTERN = re.compile(r'CVE-\d{4}-\d{4,7}')
    
    def _extract_cve(self, text: str) -> str:
        if not text:
            return None
        match = self.CVE_PATTERN.search(text)
        return match.group(0) if match else None
```

### CVSS Score Extraction

```python
class MyToolParser:
    def _extract_cvss(self, element) -> float:
        cvss_elem = element.find('cvss_score')
        if cvss_elem is not None:
            try:
                return float(cvss_elem.text)
            except ValueError:
                pass
        return None
```

---

## Performance Optimization

### Streaming for Large Files

```python
class MyToolParser:
    def parse_stream(self, file_path: str, target: str = None):
        """Parse large files using streaming"""
        context = ET.iterparse(file_path, events=('start', 'end'))
        
        for event, elem in context:
            if event == 'end' and elem.tag == 'issue':
                vuln = self._parse_vulnerability(elem, target)
                if vuln:
                    yield vars(vuln)
                elem.clear()  # Free memory
```

### Caching Parsed Results

```python
from functools import lru_cache
from hashlib import md5

class MyToolParser:
    @lru_cache(maxsize=128)
    def parse_cached(self, file_hash: str, target: str):
        # Parse only if not cached
        pass
    
    def _calculate_hash(self, file_path: str) -> str:
        with open(file_path, 'rb') as f:
            return md5(f.read()).hexdigest()
```

---

## Next Steps

- [CLI Overview](../cli/overview.md)
- [CLI Commands](../cli/commands.md)
- [Rust Integration](../dev/rust-integration.md)
- [Testing Guide](../dev/testing.md)
