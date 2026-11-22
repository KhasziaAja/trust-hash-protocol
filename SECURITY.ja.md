### セキュリティポリシーおよび機関向け Foundry アクセス規定
**ドキュメントID:** TH-SEC-01 | **レイヤー:** Tier 1（公開プロトコル仕様）

#### 1. 二層開示境界（Two-Tier Disclosure Boundary）
**Trust-Hash Protocol** の敵対的堅牢性を維持するため、本リポジトリではオープンな標準仕様と非公開のキャリブレーション基盤との間に、厳格な暗号学的および運用上の分離を設けている。
* **Tier 1（公開 / オープン仕様）：** プロトコル公理、構造ゲート方程式、THC-v1 シリアライズスキーマ、Validation API インターフェース、および Merkle-DAG 家系図メカニズムは、外部監査と相互運用性のために MIT ライセンスの下で一般公開される。
* **Tier 2（`[Foundry Core / Proprietary Calibration]`）：** 動的異常検知の重み付け、マルチモーダル交差相関閾値（$\theta_{\text{nns}}, \theta_{\text{pt}}, \theta_{\text{ao}}$）、カナリアトラップの混入分布、および Tier 1 マスター署名鍵は、リバースエンジニアリングおよび敵対的過学習を防止するため、公開リポジトリから厳格に隔離されている。

#### 2. 脆弱性開示ポリシー（Tier 1 仕様対象）
公開プロトコル仕様（TH-SPEC-01 〜 TH-SPEC-03）を対象とした形式的数理批評、暗号学的証明、およびセキュリティ監査を歓迎する。
* **機密性の低い仕様上の不備：** 標準スキーマの境界条件の曖昧さやドキュメント上の誤記については、公開 GitHub Issues またはプルリクエスト（PR）を通じて提出できる。
* **重大な暗号学的・回避脆弱性：** 三重浄化監査（TFA）構造モデル、Satori ZKP 回路、または時間遅延ミキシング（Temporal Chaffing）の匿名性保証に対する理論的または実践的なバイパス手法を発見した場合は、**エクスプロイトを一般公開してはならない**。以下の PGP 公開鍵を用いて暗号化した上で、プロトコル管理セキュリティ窓口へ報告されたい。

#### 3. 機関導入および Foundry Core アクセス
**Tier 1 Anchor** 署名ノード、高スループットの商用蒸留ファウンドリ、および保険数理リスク引受エンドポイントへのアクセスは、審査を通過した適格機関パートナー（認定研究機関、企業 R&D 部門、および規制下の金融・再保険事業体）に限定される。
ノードのオンボーディングまたは Foundry Core との統合を希望する機関は、以下の項目を記載した暗号化申請書を提出されたい。
1. 法人格の証明および登記法域（管轄地）。
2. 対象とするドメインカテゴリ（AI事前学習コーパス蒸留、R&Dテレメトリ証明、保険数理リスク引受など）。
3. ハードウェア信頼実行環境（TEE）の対応プロファイル（Intel SGX、ARM TrustZone、または同等仕様）。

#### 4. 暗号化連絡窓口および暗号学的検証
すべての機密性の高い脆弱性報告および機関導入申請は、PGP によって暗号化されなければならない。Tier 2 キャリブレーションパラメータの開示を求める平文（非暗号化）の問い合わせは、応答なく破棄される。
* **セキュリティ・機関導入窓口：** `KhasziaAja@proton.me`
* **PGP 鍵フィンガープリント：** `DA07 BEED A199 9007 EACB  A185 8841 4523 4142 CE3C`

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