# 📜 Smart Contract Audit Service

## Overview

The **Smart Contract Audit Service** provides enterprise-grade security audits for blockchain smart contracts. Supports 6+ languages, 12+ audit tools, and generates legally-defensible audit reports with AI-powered vulnerability detection.

**Location:** `cosmicsec-services/services/smart_contract_audit/`  
**Port:** 8024  
**Framework:** FastAPI + Slither + Mythril + Oyente + Echidna

## Premium Features

### 1. Multi-Language Support (6+ Languages)
- **Solidity** (Ethereum, BSC, Polygon) — Most popular
- **Vyper** (Ethereum) — Python-like syntax
- **Rust** (Solana, Near, Polkadot) — High-performance blockchains
- **Go** (Cosmos, Tendermint) — Interchain protocols
- **Cairo** (StarkNet) — ZK-rollup contracts
- **Move** (Aptos, Sui) — Resource-oriented language

### 2. Advanced Analysis Tools (12+ Tools)
| Tool | Type | Languages | Description |
|------|------|------------|-------------|
| **Slither** | Static | Solidity, Vyper | Most popular static analyzer |
| **Mythril** | Symbolic | Solidity | Symbolice execution
| **Oyente** | Symbolic | Solidity | First-generation analyzer |
| **Echidna** | Fuzzing | Solidity | Property-based fuzzing |
| **Manticore** | Symbolic | Solidity | Advanced symbolic execution |
| **Securify** | Static | Solidity | Security scanner |
| **SmartCheck** | Static | Solidity | Pattern-based analysis |
| **Solhint** | Linting | Solidity | Style and security linting |
| **Rust-SEC** | Static | Rust | Rust security audit |
| **Cargo-audit** | Vulnerability | Rust | Dependency vuln checking |
| **Gosec** | Static | Go | Go security checker |
| **Move-Prover** | Formal | Move | Formal verification |

### 3. AI-Powered Analysis
- **Vulnerability Classification** — Automatic SWC (Smart Contract Weakness Classification) mapping
- **Exploitability Scoring** — AI calculates exploit probability
- **Business Impact** — Estimates financial loss potential
- **Code Complexity** — Cyclomatic complexity analysis
- **Gas Optimization** — Suggests gas-efficient patterns

### 4. Formal Verification (Premium)
- **Mathematical Proofs** — Prove contract correctness
- **State Machine Modeling** — Model contract states
- **Invariant Checking** — Verify critical invariants
- **Theorem Proving** — Using Z3 SMT solver

### 5. Compliance Certifications
- **ERC Standards** — ERC-20, ERC-721, ERC-1155, etc.
- **SEC Compliance** — US securities regulations
- **MiCA Compliance** — EU crypto regulations
- **ISO 27001** — Information security management

## API Endpoints

### Submit Contract for Audit
```http
POST /api/v1/audit/contract
```

**Request:**
```json
{
  "contract_name": "TokenSale.sol",
  "language": "solidity",
  "source_code": "pragma solidity ^0.8.0; contract TokenSale { ... }",
  "compiler_version": "0.8.17",
  "optimization_enabled": true,
  "runs": 200,
  "libraries": [
    {"name": "OpenZeppelin", "version": "4.8.0"}
  ],
  "audit_level": "premium",  // basic, standard, premium, enterprise
  "tools": ["slither", "mythril", "echidna"],
  "enable_ai": true,
  "enable_formal_verification": true,
  "standards": ["ERC-20", "ERC-20-Permit"]
}
```

**Response:**
```json
{
  "audit_id": "audit_12345",
  "status": "queued",
  "estimated_duration": 600,
  "tools_running": ["slither", "mythril"],
  "created_at": "2026-05-05T22:00:00Z"
}
```

### Get Audit Status
```http
GET /api/v1/audit/contract/{audit_id}
```

**Response:**
```json
{
  "audit_id": "audit_12345",
  "status": "in_progress",
  "progress": 75,
  "tools_completed": ["slither", "mythril"],
  "tools_running": ["echidna"],
  "vulnerabilities_found": 8,
  "started_at": "2026-05-05T22:00:00Z"
}
```

### Get Audit Results
```http
GET /api/v1/audit/contract/{audit_id}/results
```

**Response:**
```json
{
  "audit_id": "audit_12345",
  "contract": "TokenSale.sol",
  "language": "solidity",
  "compiler": "0.8.17",
  "summary": {
    "critical": 2,
    "high": 3,
    "medium": 5,
    "low": 8,
    "gas_optimizations": 4,
    "compliance_issues": 2
  },
  "vulnerabilities": [
    {
      "id": "vuln_001",
      "severity": "critical",
      "title": "Reentrancy Vulnerability",
      "description": "The withdraw function is vulnerable to reentrancy attack...",
      "swc_id": "SWC-107",
      "cwe_id": "CWE-841",
      "location": {
        "file": "TokenSale.sol",
        "line": 45,
        "function": "withdraw"
      },
      "exploitability_score": 0.95,
      "business_impact": "Complete fund drainage possible",
      "estimated_loss": "$2.5M",
      "code_snippet": "function withdraw(uint amount) public { ... }",
      "recommendation": "Implement Checks-Effects-Interactions pattern",
      "remediation_code": "bool sent = payable(msg.sender).send(amount); require(sent);",
      "references": [
        "https://swcregistry.io/docs/SWC-107"
      ],
      "tools_detected": ["slither", "mythril"],
      "exploit_scenario": "Attacker calls withdraw multiple times before state update..."
    }
  ],
  "gas_optimizations": [
    {
      "location": "function mint()",
      "issue": "Storage read in loop",
      "potential_savings": "30% gas reduction",
      "recommendation": "Cache storage variables outside loop"
    }
  ],
  "compliance": {
    "ERC-20": {
      "status": "compliant",
      "issues": []
    },
    "SEC": {
      "status": "non-compliant",
      "issues": ["Investor accreditation not verified"]
    }
  },
  "formal_verification": {
    "status": "passed",
    "invariants_checked": 15,
    "invariants_failed": 0
  },
  "ai_analysis": {
    "risk_score": 9.2,
    "exploit_probability": 0.85,
    "complexity_score": 42,
    "recommendations": [
      "Apply ReentrancyGuard modifier",
      "Use SafeMath library",
      "Implement timelock for admin functions"
    ]
  }
}
```

### Re-Audit
```http
POST /api/v1/audit/contract/{audit_id}/re-audit
```

### List Audits
```http
GET /api/v1/audit/contracts?status=completed&limit=10
```

### Get Supported Languages
```http
GET /api/v1/audit/supported-languages
```

### Get Audit Tools
```http
GET /api/v1/audit/tools?language=solidity
```

## Audit Levels

| Level | Price | Tools | Features |
|-------|-------|-------|----------|
| **Basic** | Free | Slither | Basic vuln detection |
| **Standard** | $500 | Slither, Mythril | + Gas optimization |
| **Premium** | $2000 | 6+ tools, AI | + Formal verification, Compliance |
| **Enterprise** | $5000+ | 12+ tools, AI, Formal | + Manual review, Legal opinion |

## Smart Contract Languages

### Solidity (Ethereum, BSC, Polygon)
```solidity
// Example vulnerable contract
pragma solidity ^0.8.0;

contract VulnerableToken {
    mapping(address => uint) public balances;
    
    function withdraw(uint amount) public {
        // VULNERABLE: Reentrancy
        (bool success,) = msg.sender.call{value: amount}("");
        require(success);
        balances[msg.sender] -= amount;  // State update after external call
    }
}
```

**Audit Output:**
```json
{
  "vulnerability": "Reentrancy",
  "severity": "critical",
  "swc_id": "SWC-107",
  "remediation": "Use Checks-Effects-Interactions pattern"
}
```

### Rust (Solana, Near)
```rust
// Solana program example
use solana_program::{
    account_info::AccountInfo,
    entrypoint::ProgramResult,
    pubkey::Pubkey,
};

pub fn process_instruction(
    program_id: &Pubkey,
    accounts: &[AccountInfo],
    instruction_data: &[u8],
) -> ProgramResult {
    // Vulnerability: Missing signer check
    // Anyone can call this
    Ok(())
}
```

### Move (Aptos, Sui)
```move
module my_addr::token {
    struct Token has key {
        balance: u64,
    }
    
    public fun withdraw(account: &signer, amount: u64) acquires Token {
        let token = borrow_global_mut<Token>(signer::address_of(account));
        // Vulnerability: No balance check
        token.balance = token.balance - amount;
    }
}
```

## Formal Verification (Premium)

### Invariant Specification
```solidity
// Using SMTChecker
pragma solidity ^0.8.0;

contract VerifiedToken {
    uint public totalSupply;
    mapping(address => uint) public balances;
    
    // Invariant: sum of balances == totalSupply
    /// @custom:invariant totalSupply == sum(balances)
    function transfer(address to, uint amount) public {
        require(balances[msg.sender] >= amount);
        balances[msg.sender] -= amount;
        balances[to] += amount;
    }
}
```

### Verification Result
```json
{
  "formal_verification": {
    "prover": "Z3",
    "invariants": [
      {
        "description": "totalSupply == sum(balances)",
        "status": "proved",
        "method": "induction"
      }
    ],
    "proof_time_seconds": 12.5
  }
}
```

## AI-Powered Analysis

### Vulnerability Classification
```python
# AI analyzes vulnerability patterns
{
  "vulnerability": "Integer Overflow",
  "ai_classification": {
    "confidence": 0.98,
    "swc_mapping": "SWC-101",
    "cwe_mapping": "CWE-190",
    "exploitability": "high",
    "similar_contracts": ["0x123...", "0x456..."]
  }
}
```

### Exploitability Scoring
```json
{
  "exploitability_score": 0.95,  // 0-1 scale
  "factors": {
    "public_exploit_exists": true,
    "attack_complexity": "low",
    "privileges_required": "none",
    "user_interaction": "none"
  },
  "exploit_probability_24h": 0.75,
  "exploit_probability_7d": 0.92
}
```

### Business Impact Analysis
```json
{
  "business_impact": {
    "severity": "catastrophic",
    "estimated_loss_usd": 5000000,
    "affected_users": 50000,
    "reputation_damage": "severe",
    "regulatory_fine_risk": "high",
    "recovery_cost_usd": 500000
  }
}
```

## CLI Usage

```bash
# Submit contract for audit
cosmicsec audit submit \
  --file TokenSale.sol \
  --language solidity \
  --level premium \
  --tools slither,mythril,echidna \
  --ai-enabled

# Check audit status
cosmicsec audit status audit_12345;

# Get results
cosmicsec audit results audit_12345 --format json;

# List audits
cosmicsec audit list --status completed;

# Re-audit after fixes
cosmicsec audit re-audit audit_12345 --file TokenSale-fixed.sol
```

## Python SDK Usage

```python
from cosmicsec import CosmicSecClient

client = CosmicSecClient(api_key="cs_live_...")

# Read contract source
with open('TokenSale.sol', 'r') as f:
    source_code = f.read()

# Submit for audit
audit = client.audit.submit(
    contract_name="TokenSale.sol",
    language="solidity",
    source_code=source_code,
    compiler_version="0.8.17",
    audit_level="premium",
    tools=["slither", "mythril", "echidna"],
    enable_ai=True,
    standards=["ERC-20"]
)

print(f"Audit submitted: {audit.audit_id}")
print(f"Status: {audit.status}")

# Wait for completion
import time
while audit.status != "completed":
    audit = client.audit.get(audit.audit_id)
    print(f"Progress: {audit.progress}%")
    time.sleep(10)

# Get results
results = client.audit.get_results(audit.audit_id)
print(f"\nFound {len(results.vulnerabilities)} vulnerabilities")

for vuln in results.vulnerabilities:
    print(f"\n[{vuln.severity.upper()}] {vuln.title}")
    print(f"  SWC: {vuln.swc_id}")
    print(f"  Location: {vuln.location.file}:{vuln.location.line}")
    print(f"  Exploitability: {vuln.exploitability_score}")
    print(f"  Estimated Loss: {vuln.estimated_loss}")
    print(f"  Fix: {vuln.recommendation}")

# Check compliance
print(f"\nCompliance Status:")
for standard, result in results.compliance.items():
    print(f"  {standard}: {result['status']}")
```

## Gas Optimization (Premium)

### Common Optimizations
| Issue | Potential Savings | Recommendation |
|-------|-------------------|-----------------|
| Storage read in loop | 30-50% | Cache in memory |
| Unnecessary storage writes | 20-40% | Use `delete` to refund |
| Expensive operations in loop | 40-60% | Move outside loop |
| Using `uint256` vs `uint128` | 5-10% | Use smaller types |
| Multiple `require()` statements | 10-20% | Combine conditions |

### Example Optimization
```solidity
// Before (expensive)
function mint(uint[] calldata amounts) external {
    for (uint i = 0; i < amounts.length; i++) {
        totalSupply += amounts[i];  // Storage read+write each iteration
        balances[msg.sender] += amounts[i];  // Storage read+write
    }
}

// After (optimized)
function mint(uint[] calldata amounts) external {
    uint total = 0;
    for (uint i = 0; i < amounts.length; i++) {
        total += amounts[i];  // Memory operation (cheap)
    }
    totalSupply += total;  // Single storage write
    balances[msg.sender] += total;
}
```

## Compliance Certifications

### ERC-20 Compliance Checklist
- ✅ `totalSupply()` returns correct value
- ✅ `balanceOf()` returns correct balance
- ✅ `transfer()` returns boolean
- ✅ `transfer()` fires `Transfer` event
- ❌ `approve()` vulnerable to race condition
- ✅ `allowance()` returns correct approved amount

### SEC Compliance (US)
- ✅ Investor accreditation verification
- ❌ Missing KYC/AML checks
- ✅ Transfer restrictions implemented
- ❌ No lockup period for insiders

### MiCA Compliance (EU)
- ✅ whitepaper published
- ✅ Asset-referenced token requirements met
- ❌ Missing regulatory approval
- ✅ Investor protection measures in place

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│            Smart Contract Audit Service (8024)                │
├─────────────────────────────────────────────────────────────┤
│  API Layer (FastAPI)                                       │
│  • /audit/contract (submit, status, results)                  │
├─────────────────────────────────────────────────────────────┤
│  Analysis Engine                                         │
│  ┌────────────┬────────────┬────────────┬────────────┐  │
│  │  Slither   │  Mythril    │  Echidna   │  Manticore │  │
│  │  (Static)  │(Symbolic)  │ (Fuzzing)  │(Symbolic)  │  │
│  └────────────┴────────────┴────────────┴────────────┘  │
│  ┌────────────┬────────────┬────────────┐                │
│  │  AI Engine │Formal Verif│ Gas Opt    │                │
│  │(Risk Score)│(Z3 Prover) │(Analysis)  │                │
│  └────────────┴────────────┴────────────┘                │
├─────────────────────────────────────────────────────────────┤
│  Output Layer                                           │
│  • Vulnerability report (PDF/JSON/HTML)                  │
│  • Compliance certificate                               │
│  • Gas optimization suggestions                          │
│  • Formal verification proof (if applicable)              │
└─────────────────────────────────────────────────────────────┘
```

## Configuration

### Environment Variables
```bash
# Service
SMART_CONTRACT_AUDIT_PORT=8024
SMART_CONTRACT_AUDIT_HOST=0.0.0.0

# Tools
SLITHER_PATH=/usr/local/bin/slither
MYTHRIL_PATH=/usr/local/bin/myth
ECHIDNA_PATH=/usr/local/bin/echidna
SOLC_VERSION=0.8.17

# AI Analysis
AI_SERVICE_URL=http://ai-service:8003
AI_ANALYSIS_ENABLED=true

# Formal Verification
Z3_PATH=/usr/local/bin/z3
FORMAL_VERIFICATION_ENABLED=true

# Gas Optimization
GAS_OPTIMIZATION_ENABLED=true
OPCODE_PRICES_FILE=/app/config/opcodes.json

# Compliance
ERC_STANDARDS_DIR=/app/standards/erc
Mica_COMPLIANCE_ENABLED=true

# Database
DATABASE_URL=postgresql://user:pass@postgres:5432/cosmicsec

# Storage (for contract source)
MINIO_URL=http://minio:9000
CONTRACTS_BUCKET=smart-contracts
```

## Monitoring

### Metrics
- `audit_submissions_total` — Total audits by language
- `audit_duration_seconds` — Audit duration by level
- `vulnerabilities_found_total` — Vulns by severity
- `gas_optimizations_found_total` — Optimization suggestions

### Alerts
- Audit stuck (no progress for 2+ hours)
- Critical vulnerability found
- Compliance check failed
- Formal verification disproved (invariant violated)

## Troubleshooting

### Compilation Fails
```bash
# Check solc version
docker exec smart-contract-audit solc --version

# Install missing version
docker exec smart-contract-audit solc-select install 0.8.17

# Check source code syntax
docker exec smart-contract-audit slither TokenSale.sol --print-human-summary
```

### Tool Not Found
```bash
# Verify tool installation
docker exec smart-contract-audit which slither
docker exec smart-contract-audit which myth

# Install missing tools
docker exec smart-contract-audit pip install slither-analyzer
```

### AI Analysis Not Working
```bash
# Check AI service connectivity
curl http://ai-service:8003/health

# Verify AI_ENABLED
docker exec smart-contract-audit env | grep AI_

# Check logs
docker logs smart-contract-audit | grep "ai_analysis"
```

## Next Steps

- [3D Visualization Service](./3d-visualization.md)
- [Breach Attack Simulation](./breach-attack-sim.md)
- [Edge Computing](./edge-computing.md)
- [SLA Manager](./sla-manager.md)
- [Blockchain Security Guide](../guides/blockchain-security.md)
