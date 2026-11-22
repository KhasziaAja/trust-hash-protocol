# Standard Schema & Validation API Specification

**Document ID:** `TH-SPEC-02` | **Layer:** Tier 1 (Public Protocol Specification)

## 1. Overview & Interoperability Principles

To serve as a universal "nutritional label" for high-assurance data, the **Trust-Hash Protocol** defines a deterministic serialization format—the **Trust-Hash Certificate (`THC-v1`)**—and a stateless **Validation API**.

The schema is designed for low-latency ingestion across heterogeneous downstream consumers, including AI pre-training pipelines, R&D failure repositories, edge telemetry gateways, and actuarial risk engines.

### Compatibility with Existing Provenance Standards
Trust-Hash does not replace existing container-level signing standards (such as **C2PA**, **W3C DID**, or **X.509 PKI**). Instead, `THC-v1` operates as an orthogonal payload-process attestation layer that can be embedded directly into C2PA custom assertions or W3C Verifiable Credentials.

## 2. Standard JSON Schema (`Trust-Hash Certificate v1.0`)

Every verified dataset or telemetry stream passing the Triple-Filter Audit (TFA) is issued a cryptographically signed `THC-v1` manifest.

```json
{
  "$schema": "https://trust-hash.org/schemas/certificate/v1.0.json",
  "protocol_version": "1.0.0",
  "certificate_id": "thc:sha256:9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08",
  "issued_at": "2026-11-21T11:30:00Z",
  "assurance_tier": "TIER_1_ANCHOR",
  "payload_binding": {
    "content_hash": "sha256:e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
    "domain_category": "R_AND_D_EXPERIMENTAL_LOG",
    "privacy_mode": "ZKP_SHIELDED"
  },
  "tfa_attestation": {
    "composite_trust_score": 0.942,
    "nns_naturalness": {
      "passed": true,
      "entropy_grade": "HIGH_THERMAL_NOISE"
    },
    "pt_causal_integrity": {
      "passed": true,
      "iteration_depth": 47,
      "frontier_expansion": true
    },
    "ao_physical_alibi": {
      "passed": true,
      "tee_attestation_type": "HARDWARE_ROOTED_SGX_V2",
      "cross_modal_sync": "VERIFIED"
    }
  },
  "lineage_graph": {
    "merkle_dag_root": "bafybeigdyrzt5sfp7udm7hu76uh7y26nf3efuylqabf3oclgtqy55fbzdi",
    "parent_certificates": [
      "thc:sha256:4b227777d4dd1fc61c6f884f48641d02b4d121d3fd328cb08b5531fcacdabf8a"
    ],
    "rollup_batch_id": "zkrollup:epoch-89421"
  },
  "zkp_proof": {
    "circuit_id": "satori-zkp-groth16-v1",
    "proof_blob": "0x1a9f8c...e402b1",
    "public_inputs": [
      "0x9f86d081...",
      "0x00000001"
    ]
  },
  "issuer_signature": {
    "node_id": "did:th:verifier:ch-zurich-04",
    "algorithm": "Ed25519",
    "signature": "sig:7c9e6679742540de9448e0e6f1a1a2b3..."
  }
}
```

### 2.1 Field Definitions

| Field Path | Type | Description |
| :--- | :--- | :--- |
| `assurance_tier` | `Enum` | Verification level: `TIER_1_ANCHOR`, `TIER_2_VERIFIED`, or `TIER_3_ENTRY`. |
| `payload_binding.content_hash` | `String` | Cryptographic digest of the underlying V-Fuel dataset or ZKP commitment. |
| `payload_binding.privacy_mode` | `Enum` | `PUBLIC_CLEAR` (open dataset) or `ZKP_SHIELDED` (confidential R&D/telemetry). |
| `tfa_attestation.composite_trust_score` | `Float` | Normalized output $[0.0, 1.0]$ derived from the multiplicative TFA gate. |
| `tfa_attestation.ao_physical_alibi` | `Object` | Hardware TEE binding status confirming power, thermal, and RF cross-correlation. |
| `lineage_graph.parent_certificates` | `Array` | Upstream `certificate_id` references forming the recursive Merkle-DAG. |
| `lineage_graph.rollup_batch_id` | `String` | Temporal chaffing batch identifier preventing metadata timing correlation. |
| `zkp_proof` | `Object` | Zero-Knowledge Proof verifying TFA passage without revealing raw payload bytes. |

*(Note: Raw sensor waveforms, exact filter thresholds, and canary-trap flags are stripped prior to certificate serialization and remain isolated within `[Foundry Core / Proprietary Calibration]`.)*

## 3. Validation API Specification (REST / gRPC)

Downstream systems query the stateless **Validation API** to verify certificate authenticity, inspect lineage purity, and filter out unverified synthetic data in real time.

### 3.1 Verify Certificate Endpoint

Validates the cryptographic signature, ZKP circuit proof, and revocation/slashing status of a certificate.

* **Endpoint:** `POST /v1/certificates/verify`
* **Content-Type:** `application/json`

#### Request Payload
```json
{
  "certificate_id": "thc:sha256:9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08",
  "expected_content_hash": "sha256:e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
  "min_assurance_tier": "TIER_2_VERIFIED",
  "min_trust_score": 0.85
}
```

#### Response Payload (`200 OK`)
```json
{
  "valid": true,
  "status": "ACTIVE_VERIFIED",
  "assurance_tier": "TIER_1_ANCHOR",
  "composite_trust_score": 0.942,
  "lineage_contamination_risk": 0.0,
  "verifier_standing": "GOOD_STANDING",
  "verified_at": "2026-11-21T11:31:05Z"
}
```

### 3.2 Lineage Purity Audit Endpoint

Traverses the upstream Merkle-DAG to detect whether any ancestor node has been retroactively downgraded (e.g., due to verifier slashing or post-hoc anomaly detection).

* **Endpoint:** `GET /v1/lineage/{certificate_id}/purity`
* **Query Parameters:** `max_depth` (integer, default `10`)

#### Response Payload (`200 OK`)
```json
{
  "root_certificate_id": "thc:sha256:9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08",
  "ancestors_scanned": 14,
  "synthetic_contamination_ratio": 0.0,
  "lowest_ancestor_tier": "TIER_2_VERIFIED",
  "slashed_ancestors_detected": false
}
```

## 4. Edge Ingestion & Tier Upgrade Flow

1. **Rapid Onboarding (`Tier 3`):** External R&D tools, IDE plugins, or edge gateways submit hashed process receipts via `POST /v1/ingest/entry` to establish immediate priority timestamps.
2. **Asynchronous Distillation (`Tier 2` / `Tier 1`):** When physical TEE telemetry and iterative causal logs are attached, the payload is routed through the TFA pipeline and upgraded to a `Tier 1` or `Tier 2` certificate without breaking its original `Merkle-DAG` identity.