### Security Policy & Institutional Foundry Access
**Document ID:** TH-SEC-01 | **Layer:** Tier 1 (Public Protocol Specification)

#### 1. Two-Tier Disclosure Boundary
To preserve the adversarial integrity of the **Trust-Hash Protocol**, this repository enforces a strict cryptographic and operational separation between open standards and proprietary calibration infrastructure:
* **Tier 1 (Public / Open Specification):** Protocol axioms, structural gate equations, THC-v1 serialization schemas, Validation API interfaces, and Merkle-DAG lineage mechanics are published openly under the MIT License for public audit and interoperability.
* **Tier 2 (`[Foundry Core / Proprietary Calibration]`):** Dynamic anomaly detection weights, multi-modal cross-correlation thresholds ($\theta_{\text{nns}}, \theta_{\text{pt}}, \theta_{\text{ao}}$), canary-trap injection distributions, and Tier 1 master signing keys are strictly isolated from public repositories to prevent reverse-engineering and adversarial overfitting.

#### 2. Vulnerability Disclosure Policy (Tier 1 Specifications)
We welcome formal mathematical critiques, cryptographic proofs, and security audits targeting the open protocol specifications (TH-SPEC-01 through TH-SPEC-03).
* **Non-Sensitive Architectural Bugs:** Standard schema edge cases or documentation ambiguities may be submitted via public GitHub Issues or Pull Requests.
* **Critical Cryptographic / Bypass Vulnerabilities:** If you identify a theoretical or practical bypass against the Triple-Filter Audit (TFA) structural model, Satori ZKP circuits, or Temporal Chaffing anonymity guarantees, **do not disclose the exploit publicly**. Submit an encrypted report to the protocol stewardship security desk using the PGP key below.

#### 3. Institutional & Foundry Core Access
Access to **Tier 1 Anchor** signing nodes, high-throughput commercial distillation foundries, and actuarial risk underwriting endpoints is restricted to verified institutional partners (accredited research laboratories, enterprise R&D divisions, and regulated financial/reinsurance entities).
Organizations seeking node onboarding or Foundry Core integration should submit an encrypted inquiry containing:
1. Legal entity verification and jurisdiction of domicile.
2. Intended domain category (e.g., AI pre-training corpus distillation, R&D telemetry attestation, or actuarial underwriting).
3. Hardware Trusted Execution Environment (TEE) capability profile (Intel SGX, ARM TrustZone, or equivalent).

#### 4. Encrypted Contact & Cryptographic Verification
All sensitive vulnerability reports and institutional onboarding requests must be encrypted via PGP. Unencrypted inquiries requesting Tier 2 calibration parameters will be discarded without response.
* **Security & Institutional Desk:** `KhasziaAja@proton.me`
* **PGP Key Fingerprint:** `DA07 BEED A199 9007 EACB  A185 8841 4523 4142 CE3C`

```text
-----BEGIN PGP PUBLIC KEY BLOCK-----

xjMEawLERhYJKwYBBAHaRw8BAQdAQyBAhWSx+9U/2joh4uwJNuaXgc3G96FX
SLRorFqXyf7NK0toYXN6aWFBamFAcHJvdG9uLm1lIDxLaGFzemlhQWphQHBy
b3Rvbi5tZT7CwBAEExYKAIIFgmsCxEYDCwkHCRCIQUUjQULOPEUUAAAAAAAc
ACBzYWx0QG5vdGF0aW9ucy5vcGVucGdwanMub3Jnc+PMJPqPNAC2L+0NehP2
Xp76A1KxtbxriuRGYc9A7A4DFQoIAxYAAgIZAQKbAwIeARYhBNoHvu2hmZAH
6suhhYhBRSNBQs48AAC8AwEAjzXxD80M7SWvBQ80OVVnm4kg0JyfFZdwxJpu
azAaDoYBANIfgw2ZouFzaCgxn+WDM6BX8/O6Ev3G12m/zgleI04GzjgEawLE
RhIKKwYBBAGXVQEFAQEHQLXF0rfqlBxAjjBXvY1X7nc4Yrk3LjP8mPqATYmj
tE9IAwEKCcK+BBgWCgBwBYJrAsRGCRCIQUUjQULOPEUUAAAAAAAcACBzYWx0
QG5vdGF0aW9ucy5vcGVucGdwanMub3Jn50yg5mwf5XH3Q4muUAUBnVh2kN7J
kP0Mx6rAvXjqD7cCmwwWIQTaB77toZmQB+rLoYWIQUUjQULOPAAAMecBAPQ3
Kc402vsGNeltpCs/3wUt75qunT52pnB8EXhZHBFqAQDrOPi2alh6mRvreAqQ
1YQzhgApxbzfuyQR3XrZFVRkAg==
=70sD
-----END PGP PUBLIC KEY BLOCK-----
```