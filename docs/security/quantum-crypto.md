# Quantum Cryptography

**Quantum Cryptography Module** — NIST-approved post-quantum algorithms.

## Overview

Implements quantum-resistant cryptographic algorithms to future-proof the platform against quantum computing threats.

## Supported Algorithms

- **Kyber** (key encapsulation)
- **Dilithium** (digital signatures)
- **SPHINCS+** (hash-based signatures)
- **Hybrid schemes** (classical + post-quantum)

## Features

- Automatic algorithm negotiation
- Key rotation and lifecycle management
- Quantum-safe TLS configuration
- Compliance with NIST PQC standards

## Configuration

Environment variables:
- `QUANTUM_ALGORITHM` — algorithm to use (default: `kyber1024`)
- `QUANTUM_ENABLED` — enable quantum crypto (default: `true`)
- `HYBRID_MODE` — use hybrid classical+quantum (default: `true`)

## API Endpoints

- `POST /quantum/rotate-keys` — rotate quantum keys
- `GET /quantum/algorithms` — list supported algorithms
- `POST /quantum/sign` — quantum-sign data
