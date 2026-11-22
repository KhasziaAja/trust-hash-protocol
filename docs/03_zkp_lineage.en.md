# Recursive Data Lineage & Satori ZKP Specification

**Document ID:** `TH-SPEC-03` | **Layer:** Tier 1 (Public Protocol Specification)

## 1. Overview & Privacy-Provenance Duality

High-assurance ecosystems face a fundamental tension between **verifiability** and **confidentiality**:
* Downstream consumers (AI training pipelines, actuarial underwriters, and peer reviewers) require cryptographic proof that data passed the Triple-Filter Audit (TFA) and descends from uncontaminated primary sources.
* Upstream producers (R&D laboratories, enterprises, and edge telemetry operators) cannot expose proprietary experimental parameters, raw datasets, or operational trade secrets.

To resolve this duality, the **Trust-Hash Protocol** combines a **Recursive Merkle-DAG (Directed Acyclic Graph)** for permanent ancestry tracking with **Satori ZKP (Zero-Knowledge Proof circuits)** and **Temporal Chaffing** for payload and metadata privacy.

## 2. Recursive Data Lineage (Merkle-DAG)

Every verified dataset or telemetry artifact is assigned a node within an append-only, content-addressed **Merkle-DAG**. Unlike linear blockchains that track financial balances, the Trust-Hash lineage graph tracks **causal and thermodynamic derivation**.

```
      [Primary Sensor / Lab Run A]       [Primary Literature / Corpus B]
           (Tier 1: Anchor)                    (Tier 2: Verified)
                  │                                    │
                  └─────────────────┬──────────────────┘
                                    ▼
                     [Derived R&D Failure Analysis C]
                       • Parent Hashes: [Hash(A), Hash(B)]
                       • TFA Audit Score: 0.942
                       • ZKP Shield: Enabled
                                    │
                                    ▼
                     [Distilled V-Fuel Dataset D]
                       • Transitive Purity: 100%
                       • Lineage Depth: 2
```

### 2.1 Transitive Purity & Retroactive Slashing

1. **Parent Hash Binding:** When a new dataset $D$ is synthesized, distilled, or derived from upstream inputs $\{P_1, P_2, \dots, P_n\}$, its `Trust-Hash Certificate` must cryptographically commit to the `certificate_id`s of all parent inputs.
2. **Contamination Propagation:** If an upstream ancestor node $P_k$ is later invalidated—either due to verifier node slashing (e.g., failing a canary trap) or post-hoc anomaly audit—its degradation propagates deterministically down the DAG.
3. **Automated Quarantine:** Downstream validation queries (`GET /v1/lineage/{id}/purity`) immediately flag any descendant whose `synthetic_contamination_ratio` exceeds consumer-defined safety thresholds, preventing poisoned data from entering AI pre-training runs.

## 3. Satori ZKP Circuit Architecture

**Satori ZKP** enables an organization to prove that a private dataset $X$ satisfies all TFA predicates without revealing $X$ to the verifier network or the public ledger.

### 3.1 Prover-Verifier Relation

Let $X$ denote the private raw payload (e.g., proprietary chemical synthesis logs or clinical telemetry), $W_{\text{phys}}$ denote the TEE-signed hardware telemetry (power, thermal, and RF traces), and $\pi$ denote the generated zero-knowledge proof.

The circuit $\mathcal{R}_{\text{TFA}}$ enforces the following statement:

$$\exists (X, W_{\text{phys}}) \quad \text{s.t.} \quad \begin{cases} \text{Commit}(X, r) = C_X \\ \text{VerifyTEE}(W_{\text{phys}}, \text{pk}_{\text{hw}}) = 1 \\ \Phi(NNS(X)) \ge \theta_{\text{nns}} \\ \Delta I(PT(X)) \ge \theta_{\text{pt}} \\ \mathcal{C}(W_{\text{phys}}) \ge \theta_{\text{ao}} \end{cases}$$

* **Public Inputs:** Commitment hash $C_X$, hardware root public key $\text{pk}_{\text{hw}}$, assurance tier classification, and parent Merkle-DAG root.
* **Private Witnesses:** Raw payload $X$, blinding factor $r$, intermediate R&D state transitions, and raw multi-modal sensor waveforms $W_{\text{phys}}$.

*(Note: Dynamic predicate thresholds* $\theta_{\text{nns}}, \theta_{\text{pt}}, \theta_{\text{ao}}$ *are injected via blind commitment updates from `[Foundry Core / Proprietary Calibration]` so circuit verification remains decoupled from static public constants.)*

## 4. Metadata Side-Channel Defense: Temporal Chaffing

Even when raw payloads are shielded by Zero-Knowledge Proofs, publishing individual certificate commitments in real time leaks sensitive operational metadata—such as R&D cadence, compute expenditure bursts, and graph topology correlations between organizations.

To neutralize side-channel inference, Trust-Hash implements **Temporal Chaffing & Zero-Knowledge Rollups**:

```
[Org A Proof Request] ──┐
[Org B Proof Request] ──┼──► [Randomized Time-Window Buffer] ──► [ZK-Rollup Aggregator] ──► [Public Merkle-DAG]
[Dummy Chaff Proofs]  ──┘         (Breaks Timestamp Sync)           (Single Batch Root)
```

1. **Time-Window Buffering:** Incoming proof submissions are held within randomized epoch windows rather than committed immediately upon receipt.
2. **Chaff Injection:** The rollup aggregator mixes authentic proof submissions with cryptographically indistinguishable, zero-weight dummy proofs (chaff) to normalize network traffic volume.
3. **Batch State Commitment:** A single recursive ZK-Rollup proof commits the entire mixed batch to the public lineage ledger, severing timestamp correlation between upstream parent references and downstream corporate activity.

## 5. Attribution & Royalty Routing Compatibility

While Trust-Hash does not mandate a specific native cryptocurrency, the Merkle-DAG structure provides deterministic cryptographic attribution weights across ancestor nodes. External licensing, data-market, and actuarial settlement systems may traverse the DAG to distribute royalties proportionally to verified primary data contributors.