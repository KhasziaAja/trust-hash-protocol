# Contributing to Trust-Hash Protocol

We welcome formal mathematical critiques, cryptographic proofs, schema refinements, and reference implementations from researchers, cryptographers, systems engineers, and legal scholars.

## 1. Two-Tier Disclosure Boundary
Before submitting any contribution, please note the architectural separation defined in `SECURITY.md`[cite: 12]:
* **Tier 1 (Public Specification — This Repository):** Open protocol axioms, structural equations, THC-v1 serialization schemas, Validation API interfaces, and Merkle-DAG lineage mechanics[cite: 12]. All contributions here are licensed under the **MIT License**[cite: 12].
* **Tier 2 (`[Foundry Core / Proprietary Calibration]`):** Dynamic cutoff thresholds ($\theta_{\text{nns}}, \theta_{\text{pt}}, \theta_{\text{ao}}$), feature-weighting matrices, and canary-trap injection rates are intentionally excluded from this repository to prevent adversarial overfitting[cite: 12]. Pull requests proposing static hardcoded thresholds for production deployment will not be merged into Tier 1 specifications.

## 2. Priority Research Areas
Please review `ISSUES.en.md` (Issues #01 through #10) for open engineering, cryptographic, and institutional challenges[cite: 3, 9]. When submitting a Pull Request addressing an open issue:
1. Reference the corresponding issue identifier (e.g., `Addresses Issue #02`) in your PR title and description.
2. Provide formal notation, complexity analysis, or reproducible test vectors where applicable.
3. Ensure bilingual consistency if modifying core specification documents (`*.en.md` and `*.ja.md`)[cite: 2, 3].

## 3. Security Vulnerabilities
**Do not open public GitHub Issues for critical cryptographic or bypass vulnerabilities** (such as TFA circumvention, Satori ZKP soundness flaws, or Temporal Chaffing deanonymization)[cite: 12]. Follow the encrypted PGP disclosure procedure outlined in `SECURITY.md`[cite: 12].