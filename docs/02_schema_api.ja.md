# 標準JSONスキーマおよび Validation API 仕様書

**ドキュメントID:** `TH-SPEC-02` | **レイヤー:** Tier 1（公開プロトコル仕様）

## 1. 概要と相互運用性の原則

高い保証水準が求められるデータに対する普遍的な「栄養成分表示ラベル」として機能するため、**Trust-Hash Protocol** は決定論的なシリアライズ形式である **Trust-Hash Certificate（`THC-v1`）** と、ステートレスな **Validation API（検証API）** を定義する。

本スキーマは、AI事前学習パイプライン、R&D失敗データリポジトリ、エッジテレメトリゲートウェイ、および保険数理リスクエンジンを含む、多様な下流消費者による低遅延な摂取（インジェスチョン）を前提に設計されている。

### 既存の来歴証明規格との互換性

Trust-Hashは、既存のコンテナレベル署名規格（**C2PA**、**W3C DID**、**X.509 PKI** など）を置き換えるものではない。代わりに、`THC-v1` は直交する「ペイロード精製プロセス証明レイヤー」として機能し、C2PAのカスタムアサーションや W3C Verifiable Credentials の内部に直接埋め込むことが可能である。

## 2. 標準JSONスキーマ（`Trust-Hash Certificate v1.0`）

三重浄化監査（TFA）を通過したすべての検証済みデータセットまたはテレメトリストリームには、暗号学的に署名された `THC-v1` マニフェストが発行される。

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

### 2.1 フィールド定義

| フィールドパス | 型 | 説明 |
| :--- | :--- | :--- |
| `assurance_tier` | `Enum` | 検証保証レベル：`TIER_1_ANCHOR`、`TIER_2_VERIFIED`、または `TIER_3_ENTRY`。 |
| `payload_binding.content_hash` | `String` | 対象となる V-Fuel データセットの暗号学的ダイジェスト、または ZKP コミットメント値。 |
| `payload_binding.privacy_mode` | `Enum` | `PUBLIC_CLEAR`（公開データセット）または `ZKP_SHIELDED`（機密 R&D・テレメトリ）。 |
| `tfa_attestation.composite_trust_score` | `Float` | 乗算型の TFA ゲートから算出された正規化信頼スコア $[0.0, 1.0]$。 |
| `tfa_attestation.ao_physical_alibi` | `Object` | 消費電力、熱拡散遅延、および環境 RF の交差相関を確認するハードウェア TEE バインディング状態。 |
| `lineage_graph.parent_certificates` | `Array` | 再帰的 Merkle-DAG を構成する上流の親 `certificate_id` 参照配列。 |
| `lineage_graph.rollup_batch_id` | `String` | メタデータのタイミング相関を断ち切るための時間遅延ミキシング（Temporal Chaffing）バッチ識別子。 |
| `zkp_proof` | `Object` | 生のペイロードバイト列を開示せずに TFA 通過を証明するゼロ知識証明オブジェクト。 |

*（※注記：生のセンサー波形データ、正確なフィルター閾値、およびカナリアトラップ判定フラグは証明書のシリアライズ前に除去され、`[Foundry Core / Proprietary Calibration]` 内に隔離保持される。）*

## 3. Validation API 仕様（REST / gRPC）

下流のシステムは、ステートレスな **Validation API** へ照会することで、証明書の真正性検証、情報家系図（リネージ）の純度監査、および未検証の合成データのリアルタイム除外を行う。

### 3.1 証明書検証エンドポイント（Verify Certificate）

証明書の暗号学的署名、ZKP 回路証明、および失効・スラッシング状態を検証する。

* **エンドポイント:** `POST /v1/certificates/verify`
* **Content-Type:** `application/json`

#### リクエストペイロード

```json
{
  "certificate_id": "thc:sha256:9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08",
  "expected_content_hash": "sha256:e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
  "min_assurance_tier": "TIER_2_VERIFIED",
  "min_trust_score": 0.85
}
```

#### レスポンスペイロード（`200 OK`）

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

### 3.2 家系図純度監査エンドポイント（Lineage Purity Audit）

上流の Merkle-DAG を遡及走査し、検証ノードのスラッシングや事後的な異常検知によって遡及格下げされた祖先ノードが含まれていないかを検出する。

* **エンドポイント:** `GET /v1/lineage/{certificate_id}/purity`
* **クエリパラメータ:** `max_depth`（整数、デフォルト値 `10`）

#### レスポンスペイロード（`200 OK`）

```json
{
  "root_certificate_id": "thc:sha256:9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08",
  "ancestors_scanned": 14,
  "synthetic_contamination_ratio": 0.0,
  "lowest_ancestor_tier": "TIER_2_VERIFIED",
  "slashed_ancestors_detected": false
}
```

## 4. エッジ摂取およびティア昇格フロー

1. **迅速な初期オンボーディング（`Tier 3`）：** 外部の R&D ツール、IDE プラグイン、またはエッジゲートウェイは、`POST /v1/ingest/entry` を介してハッシュ化されたプロセス領収書を送信し、即座に先願タイムスタンプを確立する。
2. **非同期蒸留とアップグレード（`Tier 2` / `Tier 1`）：** 物理 TEE テレメトリや反復的な因果ログが後から付与された際、ペイロードは TFA パイプラインへとルーティングされ、元の `Merkle-DAG` の系譜同一性を維持したまま `Tier 1` または `Tier 2` 証明書へと昇格する。