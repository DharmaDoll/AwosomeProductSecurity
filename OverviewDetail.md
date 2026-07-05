
プロダクトセキュリティは、単なる脆弱性診断やインシデント対応ではなく、要件・設計・実装・ビルド・デプロイ・運用・対外公開・継続改善を通貫で扱う業務体系です。NIST SSDF は secure SDLCへの統合を、NIST CSF 2.0 はガバナンスを中心に置いた全体統制を、JPCERT/CCとFIRST はPSIRTを製品起因リスクに特化した独立機能として整理しています。

実務上は、アプリケーション、プラットフォーム/インフラ、運用、対外、PSIRT/脆弱性管理、ガバナンス、教育・文化の各レイヤーに責任を分けると、抜け漏れが減り、担当組織と成果物を定義しやすくなります。特に、アプリ層の「安全な変更」、プラットフォーム層の「改ざん耐性ある供給」、運用層の「検知と復旧」、PSIRT 層の「受理・評価・修正・公開」が分離されていることが重要です。

近年は、SBOM、署名付き provenance、CSAF/VEXのような機械可読な成果物が、サプライチェーン管理・顧客回答・脆弱性優先順位付けの共通言語になりつつあります。METIはSBOM導入を段階的に整理し、OASIS CSAF は advisory の構造化交換を定義し、EUCRA は製品ライフサイクル全体での脆弱性対応をメーカーの義務として位置付けています。

推奨 KPIは、網羅率、早期検出率、修正速度、例外の健全性、教育の定着に寄せるべきです。すなわち、「どれだけやったか」だけでなく、「本番に漏れたか」「公表が遅れたか」「同じ欠陥が繰り返されたか」を測る設計が必要です。これはNIST のインシデント対応、OWASP SAMM、DSOVS、NICE Framework の考え方とも整合します。


### レイヤー別全体像

| レイヤー | 主責任 | 主要成果物 | 主担当の例 |
| :--- | :--- | :--- | :--- |
| **アプリケーション** | 安全な要件化・設計・実装・検証 | セキュリティ要件、脅威モデル、コードスキャン結果、DAST/API テスト結果 | 開発チーム、アーキテクト、AppSec、QA |
| **プラットフォーム/インフラ** | 改ざんされにくい開発・ビルド・配備基盤の提供 | 保護済み CI/CD、SBOM、署名、provenance、ポリシー設定 | Platform Eng、SRE、CloudSec、IAM 管理者 |
| **運用** | 可観測性、検知、封じ込め、復旧 | ログ設計、検知ルール、IR Runbook、ポストモーテム | SRE、SecOps/SOC、IR Manager、当番開発者 |
| **対外** | サプライチェーン、顧客、監査人への説明責任 | 供給者台帳、SBOM/VEX 受領記録、質問票回答、Trust Center 資料 | 調達、法務、セキュリティ保証、営業技術、サポート |
| **PSIRT/脆弱性管理** | 発見・受付・評価・修正調整・公開 | ケース記録、CVE/CVSS、修正計画、アドバイザリ、CSAF/VEX | PSIRT、開発責任者、法務、広報、サポート |
| **ガバナンス/ポリシー/コンプライアンス** | リスク方針、標準、例外、監査証跡 | 標準、RACI、例外台帳、KPI ダッシュボード、統制証跡 | CISO、GRC、法務、内部監査、PMO |
| **教育・文化** | 役割定義、育成、習慣化、学習ループ | スキルマップ、研修記録、Champion 名簿、演習結果 | Engineering Manager、Security Champion、HR/L&D、AppSec |

---

## 規模別推奨運営モデル

規模が未指定のため、以下のような規模別推奨で考えるのが実務的です。これはCSF/SAMM/DSOVS/NICEを踏まえた実装上の推奨であり、厳密な標準要求ではありません。

| 規模 | 推奨運営モデル | 最低限の分離 |
| :--- | :--- | :--- |
| **小規模** | 開発チーム主体。Security Champion を各主要プロダクトに置き、AppSec/PSIRT/GRC は兼務で開始 | 「開発責任者」と「最終公開判断者」を分ける |
| **中規模** | AppSec、Cloud/Platform Security、SecOps、PSIRTの責任者を分け、中央標準を各チームに適用 | PSIRT と通常サポート窓口を分ける |
| **大規模** | AppSec、Product Sec Architecture、CloudSec、SecOps、PSIRT、GRCを独立させ、policy-as-code と advisory 自動化を前提化 | 例外承認、修正優先順位付け、対外公開判断を分権しすぎない |

> **基準モデルについて**
> 複数の標準を実務フローに落としたときの、レイヤー横断の基準モデルです。SSDFの開発工程、OWASP の脅威モデリング、SLSA の provenance、NIST の incident response、PSIRTの公開調整を一続きの運用にしています。
> *(ワークフロー：モデリング ➔ SAST、検査、保存 ➔ SBOM生成、署名 ➔ ポリシー適用 ➔ Advisory VEX)*

---

## 各レイヤーの実務整理

### 1. アプリケーション層

このレイヤーの中心は、「要件・設計・コードにセキュリティを埋め込む」ことです。NIST SSDF は secure development practices を SDLCに統合することを求め、OWASP ASVS は要求事項の具体化、OWASP Threat Modeling Cheat Sheet は設計段階の基本手順、Microsoft SDL は設計・実装・検証・リリースまでの必須チェックを提示しています。GitHub CodeQL、Secret Scanning、GitLab DASTは、この層の自動化例として実装しやすい一次情報です。

| 属性 | 実務整理 |
| :--- | :--- |
| **主な業務一覧** | セキュリティ要求定義、攻撃面整理、脅威モデリング、セキュア設計レビュー、コーディング規約適用、PR時レビュー、SAST、秘密情報検査、依存関係レビュー、DAST/API テスト、リリース前レビュー。 |
| **担当組織/役割** | プロダクトマネージャーが要求を明文化し、アーキテクトが設計責任、開発チームが実装責任、AppSecが標準/レビュー支援、QAが動的検証を担うのが基本形です。これは Microsoft SDLのphase と supporting activities を踏まえた実務上の割当です。 |
| **具体的な活動フロー** | 重要機能はまずASVS などの要求ベースラインに落とし込み、次に「分解→脅威特定と優先順位付け→軽減策→レビュー/妥当性確認」の順で脅威モデリングを行い、PRでCodeQL, Secret Scanning, Dependency Review を通し、リリース前に DAST/API セキュリティ試験で未検出リスクを拾います。 |
| **成果物** | セキュリティ要求一覧、脅威モデル、設計判断記録、レビューチェック結果、コードスキャンアラート、動的テスト結果、残留リスク/例外記録。 |
| **推奨ツール/技術例** | 要求定義は OWASP ASVS 5.0、設計は Microsoft Threat Modeling Tool または OWASP Threat Dragon、静的解析はGitHub CodeQL、秘密情報検査は GitHub Secret Scanning、動的検証は GitLab DAST / API Security Testing が分かりやすい組み合わせです。公式日本語があるものは優先すると定着しやすいです。 |
| **成功指標 KPI 例** | 高リスク機能の脅威モデル実施率、重要/高findings のマージ前検出率、PRあたりのセキュリティ差し戻し率、本番流出した秘密情報の件数、リリース後初報脆弱性件数/リリース、設計レビュー完了までのリードタイム。これらは SSDF/SDL/ASVS が要求する活動を計測可能にした指標です。 |
| **よくある課題と対策** | 典型課題は、要求が曖昧で設計レビューが形骸化すること、SASTのノイズで開発者が疲弊すること、秘密情報検査が「事故後対応」化することです。対策として、ASVSを製品共通ベースラインにし、脅威モデリングを「高リスク変更のみ必須」に絞り、スキャン結果は severity と再現性で gate を分けるのが有効です。Secret Scanning は有効性チェックとローテーション運用まで含めます。 |

#### アプリケーション層 実務チェックリスト
- [ ] 高リスク機能にセキュリティ要求と受入条件がある
- [ ] 脅威モデリング対象の判定基準がある
- [ ] PRでSAST・秘密情報検査・依存関係レビューが動く
- [ ] DAST/API テストを本番相当環境で定期実行している
- [ ] 例外承認に期限と責任者がある
- [ ] 重大 findings が backlog ではなく release gate に結び付いている

---

### 2. プラットフォームとインフラ層

このレイヤーの責務は、「安全に作れる・安全に届けられる基盤」を標準化することです。SLSA は provenance と build trust を段階化し、in-toto は supply chain のどの工程を誰が行ったかを検証可能にし、Sigstore Cosign は成果物署名を、SyftはSBOM生成を、Microsoft Security DevOps は build pipelineへの静的解析統合を実装例として示します。Google BeyondProd と Security Command Center の CI/CD integration は、クラウドネイティブ環境での境界防御依存からの脱却と、パイプライン接続の統制を示しています。

| 属性 | 実務整理 |
| :--- | :--- |
| **主な業務一覧** | リポジトリ保護、ブランチ保護、パイプライン最小権限化、シークレット管理、ビルド環境分離、SBOM 自動生成、署名/attestation、laC ポリシー、イメージ/ランナー更新、開発端末基準化、環境差分管理。 |
| **担当組織/役割** | Platform Engineering がテンプレートと golden path を提供し、Cloud/Infra Security が基準を定義し、SREが運用し、IAM 管理者が権限制御、開発チームは標準テンプレートを利用するのが効率的です。これはSLSA・DSOVS・クラウドベンダーの責任分担を踏まえた推奨です。 |
| **具体的な活動フロー** | まず CI/CD テンプレートを標準化し、依存関係レビューとシークレット検査をPRに入れ、ビルド時にSBOMを生成し、artifact に署名と provenance を付け、デプロイ時に laC/クラウドポリシーで逸脱を止め、配備後に構成準拠を継続監視します。SLSA は provenance の存在から hardened build までの成熟段階を、GoogleとAzure は CI/CDと policy の統合を示しています。 |
| **成果物** | 保護済み pipeline テンプレート、SBOM、署名 bundle、build provenance、基準準拠レポート、更新済みベースイメージ、アクセス権設計、例外記録。 |
| **推奨ツール/技術例** | SLSA、in-toto、Sigstore Cosign、Syft、Microsoft Security DevOps、GitHub Dependency Review、Security Command Center CI/CD integration が一次情報に基づく実装候補です。クラウドネイティブ設計思想としては BeyondProd が参照しやすいです。 |
| **成功指標 KPI 例** | 保護済みリポジトリ率、SBOM添付率、署名付き release artifact 比率、provenance 検証成功率、高権限 runner の数、ベースイメージ更新遅延日数、CI/CD トークンの長期資格情報比率。これらは build integrity とdriftを直接見ます。 |
| **よくある課題と対策** | 課題は、共有 runner や broad token による build 改ざん面、SBOM は作るが更新・配布しない点、開発環境と本番で別の基準が走る点です。対策は、hosted/隔離 build、OIDCなどの短命資格情報、artifact 署名必須化、SBOMを release asset として常時配布、policy-as-code の中央管理です。 |

#### プラットフォームとインフラ層 実務チェックリスト
- [ ] 全リポジトリに branch protection と required checks がある
- [ ] CI/CDで長期シークレットを減らし、短命資格情報へ移行している
- [ ] すべての正式リリースにSBOM が付く
- [ ] 重要 artifact に署名と provenance が付く
- [ ] ベースイメージと runner に更新 SLAがある
- [ ] laC/クラウド設定の逸脱を deployment 前後で検出できる

---

### 3. 運用層

運用層の目的は、「事故を起こさない」だけではなく、「起きても検知し、封じ込め、学習する」ことです。NIST SP 800-61 Rev.3 は incident response を CSF 2.0 全体に組み込むべきだと整理し、AWSは detective controls を CloudTrail, CloudWatch, AWS Config, GuardDuty で構成し、Google Cloud は Audit Logs の有効化・集約・分析の三段階を示し、Azure は Monitor, Log Analytics, Policy, Update Manager を中心にセキュア運用を構成しています。

| 属性 | 実務整理 |
| :--- | :--- |
| **主な業務一覧** | テレメトリ要件定義、監査ログ有効化、ログ集約、検知ルール開発、アラートトリアージ、封じ込め、復旧、証跡保全、ポストモーテム、改善のbacklog化。 |
| **担当組織/役割** | SRE が可観測性設計と復旧責任、SecOps/SOCが監視と初動、IR Manager が指揮、開発チームがサービス文脈と再発防止、サポートが顧客影響確認を担当する形が一般的です。これは NIST IR と主要クラウド運用ガイドを踏まえた推奨です。 |
| **具体的な活動フロー** | まず「何を守るか」に応じて必要ログを決め、監査ログとアプリログを中央集約し、検知コンテンツで triageし、影響範囲を判断して containment/recoveryを行い、最後に学習事項を runbook と設計へ戻します。Google Cloud は enable, route, analyze の3ステップ、AWS は detective controls、NIST は incident lifecycle 全体の効率化を示しています。 |
| **成果物** | ログ設計書、監査ログ設定、検知ルール、当番 Runbook、インシデ延トタイムライン、復旧手順、ポストモーテム、是正アクション台帳。 |
| **推奨ツール/技術例** | AWS CloudTrail / CloudWatch / AWS Config / GuardDuty、Google Cloud Audit Logs / Cloud Logging / Security Command Center、Azure Monitor / Log Analytics / Azure Policy / Azure Update Manager。ログ分析先はSIEMを固定せず、まず「中央集約・権限制御・保管期間・相関」を満たすことを優先すべきです。 |
| **成功指標 KPI 例** | 重要ログ有効化率、MTTD、封じ込めまでの時間、重大アラートの誤検知率、ポストモーテムの30日以内クローズ率、パッチ適用遅延、証跡欠損インシデント数。Update Manager や Policy のような統制系も KPI に含めるのが有効です。 |
| **よくある課題と対策** | よくある失敗は、「ログはあるが見ていない」「コスト回避で必要ログが無効」「アラートにサービス所有者情報がない」ことです。GCPでは Data Access audit logs が明示的有効化必要で、Azureでは中央 log management が推奨され、AWSは detective controls を foundational としています。対策は、クリティカルログの最低基準、所有者メタデータの付与、相関ルールの定期棚卸しです。 |

#### 運用層 実務チェックリスト
- [ ] 重要サービスごとに「必須ログ一覧」がある
- [ ] 監査ログとアプリログが中央で相関できる
- [ ] 重大ケースの封じ込め Runbook が演習済みである
- [ ] ポストモーテムが blame-freeで、設計/監視改善に戻る
- [ ] ログ保管期間と証跡保全ルールが法務と整合している
- [ ] パッチ適用や回復手順が変更管理と結び付いている

---

### 4. 対外レイヤー と PSIRT/脆弱性管理レイヤー

この二つは近いようで違います。対外レイヤーは「供給者・顧客・監査人との関係管理」、PSIRT レイヤーは「製品脆弱性そのものの発見・評価・修正・公開」です。NIST 800-161はサプライチェーンリスクを組織全体で管理すべきとし、METIはSBOMを環境構築・作成共有・運用管理の段階に分け、JPCERT/CC は PSIRT を製品起因リスクに対応する組織内機能と整理しています。OASIS CSAFとVEXは、両レイヤーを結ぶ機械可読な成果物です。

#### 対外レイヤーの整理

| 属性 | 実務整理 |
| :--- | :--- |
| **主な業務一覧** | 供給者のtiering、調達前審査、契約条項、SBOM/VEX 受領、サードパーティ脆弱性追跡、顧客質問票対応、Trust Center/公開資料整備、監査応対。 |
| **担当組織/役割** | 調達・Vendor Management、Security Assurance、法務、営業技術、サポート、プロダクト責任者。小規模組織では法務/調達/セキュリティの兼務でも始められますが、対外回答の最終承認者は明確化すべきです。 |
| **具体的な活動フロー** | 供給者を接続先・権限・重要度でtier分けし、契約時に通知 SLA, SBOM・重大脆弱性連絡条項を入れ、受領した SBOM/VEXを内部台帳へ取り込み、顧客からは標準化した証跡パックで回答し、定期に再評価します。METIはSBOM をフェーズ分けし、NISTはC-SCRM の戦略・計画・評価を求めています。 |
| **成果物** | 供給者台帳、tier 分類、契約テンプレート、質問票回答集、SBOM/VEX 受領記録、Center FAQ、顧客向け advisory テンプレート。 |
| **推奨ツール/技術例** | METI SBOM 導入手引、OWASP Dependency-Track、Syft、CSAF 2.0 / VEX、Open Source Insights。Dependency-Track は SBOMとEPSS/VEXを組み合わせた triage を支援し、deps.dev はOSS 依存関係の構造理解に向きます。 |
| **成功指標 KPI** | Tier 1供給者の審査完了率、SBOM受領率、重大通知 SLAを契約化した比率、顧客質問票の中央値回答日数、重要OSSの所有者不明率。これらは visibility と応答速度を測ります。 |
| **よくある課題と対策** | 典型課題は、供給者ごとにフォーマットが違い比較できないこと、SBOMを受け取っても運用に取り込まれないこと、顧客質問票が都度ゼロから作られることです。対策は、CycloneDX/CSAF/VEXなど機械可読形式への寄せ、標準証跡パック化、受領情報を vulnerability management に連動させる運用設計です。 |

#### PSIRT/脆弱性管理レイヤーの整理

| 属性 | 実務整理 |
| :--- | :--- |
| **主な業務一覧** | 報告受付、受領確認、再現・真偽判定、影響範囲分析、優先順位付け、修正調整、CVE/CVSS付与、advisory 公開、顧客通知、再発防止。FIRST/JPCERT は PSIRTを、製品脆弱性起因リスクへの専用機能と位置付けています。 |
| **担当組織/役割** | PSIRT Lead、開発責任者、QA/検証、法務、広報、サポート、場合によっては CNA Coordinator。一般問い合わせ窓口とPSIRT を混同しないのが実務上重要です。 |
| **具体的な活動フロー** | 受理→受付確認→再現 暫定深刻度→修正計画→公開判断→アドバイザリ/CSAF/VEX 発行→顧客周知→クローズ、の順で進めるのが基本です。評価にはCVSSでseverity、EPSSで exploit probability、KEVで既知の悪用有無を重ねると、修正優先順位が現実的になります。CVSS 自体も Threat/Environmental を消費者側で上書きすべきと明記しています。 |
| **成果物** | ケース台帳、再現手順、影響評価、CVSS vector、優先順位付け根拠、修正計画、公開 advisory、CVE、CSAF/VEX、事後レビュー。 |
| **推奨ツール/技術例** | PSIRT Services Framework / Maturity Document、CVSS v4.0、EPSS、KEV Catalog、CSAF 2.0/VEX。企業がCNAであれば CVE 割当運用も포함（包含）します。Panasonic のように CNA 化して advisory を公開している例は、国内でも参考になります。 |
| **成功指標 KPI 例** | 受付確認までの時間、真偽判定までの時間、severity 別の修正完了日数、公開までの lead time、CVE/CVSS付与率、機械可読 advisory 公開率、再オープン率。PSIRT Maturity の考え方に沿って、初期はSLAと公開継続性、成熟後は自動化率を見るとよいです。 |
| **よくある課題と対策** | 課題は、再現環境がなく triage が止まること、製品オーナー不明で fix coordination が遅れること、法務/広報との公開判断が長引くことです。対策は、製品台帳の整備、severity committee の設置、責任ある開示ポリシー公開、公開テンプレートと承認フローの事前合意です。Ciscoのような定時 disclosure と機械可読APIは、運用品質を高める好例です。 |

> **PSIRT 実務運用フロー**
> 脆弱性受理（研究者、顧客、内部検知） ➔ 受付確認 ➔ 再現/真偽判定 ➔ 影響製品・バージョン特定 ➔ 優先順位付け（CVSS + EPSS + KEV + 顧客影響） ➔ 修正方針決定（パッチ・緩和策、回避策） ➔ 修正/検証 ➔ 公開調整（CVE Advisory CSAF/VEX） ➔ 顧客通知・サポート更新 ➔ クローズ/再発防止

#### PSIRT/対外レイヤー 実務チェックリスト
- [ ] 研究者・顧客・社内向けの受付窓口が公開されている
- [ ] 受付確認 SLAと severity 別修正 SLA がある
- [ ] CVSSだけでなく exploitability と環境影響を見ている
- [ ] 公開テンプレートがあり、法務/広報承認が事前定義されている
- [ ] SBOM/VEX/CSAF を顧客対応に使える形で保持している
- [ ] 供給者起因の脆弱性と自社実装脆弱性を分けて追跡できる

---

### 5. ガバナンスとポリシーとコンプライアンス

NIST CSF 2.0 は GOVERN を中心に置き、リスク戦略、期待、ポリシー、役割、責任、権限、サイバーサプライチェーン管理を他機能の前提としています。OWASP SAMMとDSOVSは、これをソフトウェア保証プログラムの成熟とギャップ分析に落とし込めるようにしています。したがって、プロダクトセキュリティのガバナンスは「ルール作成」ではなく、製品群をどうtier分けし、何を標準、何を例外にし、何を計測するかの設計そのものです。

| 属性 | 実務整理 |
| :--- | :--- |
| **主な業務一覧** | リスク許容度定義、製品分類、Secure SDLC 標準化、例外承認、統制証跡設計、経営向け KPI、顧客要求や法規の差分管理、監査対応。 |
| **担当組織/役割** | CISO/CSO、GRC、法務、内部監査、Architecture Review Board、PMO、各プロダクト責任者。CSF 2.0 は roles, responsibilities, and authorities を GOVERN に含めています。 |
| **具体的な活動フロー** | 事業・市場・データ・可用性で製品をtier分けし、tierごとに required controls を定義し、ASVS/SSDF/SLSA/IR などの標準を内部標準にマッピングし、例外は期限付きで承認し、四半期単位で KPIと監査証跡をレビューする流れが実務的です。SAMM/DSOVS はこの成熟改善に向きます。 |
| **成果物** | セキュリティ標準、製品 tier 定義、RACI、例外台帳、コントロールマッピング、経営ダッシュボード、監査証跡、顧客保証資料。 |
| **推奨ツール/技術例** | NIST CSF 2.0 を上位構造に、OWASP SAMM を成熟度評価に、DSOVS を DevSecOps ギャップ分析に使う構成が扱いやすいです。クラウド実装面では Azure Policy や AWSの継続的コンプライアンス機能を policy-as-code の例として使えます。 |
| **成功指標 KPI 例** | Tier 定義済みプロダクト比率、例外の平均滞留日数、監査証跡の自動取得率、重大監査指摘の是正完了率、製品標準への適合率、経営レビュー実施率。数だけでなく「期限切れ例外の放置」を重視すべきです。 |
| **よくある課題と対策** | 失敗しやすいのは、全製品に同じ重さの統制をかけて開発を遅くすること、監査証跡が手作業で維持不能になること、法規対応が「後付け」になることです。対策は、tiering と minimum baseline、evidence automation、policy-as-code の採用です。Azure Policyのようはサービスは、診断設定や更新管理を準拠評価に結び付けやすいです。 |

#### 規制・法令をどう扱うべきか
業界と市場が未指定なので、ここでは規模別ではなく市場別の注意点だけを簡潔に示します。EU向けのデジタル製品では、CRA が planning/design/development/maintenance を通じた mandatory requirements と vulnerability handling を求め、2026年9月から報告義務、2027年12月から主要義務が適用されます。NIS2は18分野にわたり統一的なサイバー義務を課します。これらは日本国内専売製品に直ちに一般適用されるものではありませんが、EU市場を持つプロダクト組織には設計インプットとして極めて重要です。

#### ガバナンス層 実務チェックリスト
- [ ] 製品の risk tier が定義されている
- [ ] tierごとに required controls が違う
- [ ] 例外は有効期限と代替対策を持つ
- [ ] 指標は経営・開発・セキュリティで同じ定義を使う
- [ ] 規制要件が「法務メモ」ではなく開発標準に反映されている
- [ ] 監査証跡の収集が手作業前提になっていない

---

### 6. セキュリティ教育と文化とその他

教育・文化は、他レイヤーの“補助”ではなく、全レイヤーの実行可能性を左右する基盤です。NICE Framework はサイバー業務の共通言語を、OWASP の DevSecOps Guideline は 「everyone is responsible for security」を、SAMM は Governance 配下の Education & Guidance を、CISA Secure by Design は測定可能な改善行動を提示しています。つまり、教育とは「全員に同じ研修を受けさせる」ことではなく、役割ごとに必要な判断力を与えることです。

| 属性 | 実務整理 |
| :--- | :--- |
| **主な業務一覧** | 役割定義、研修計画、Security Champion 制度、演習、Secure Coding/Threat Modeling 教育、ポストモーテム学習、採用要件への反映。 |
| **担当組織/役割** | Engineering Manager、Security Champion、AppSec、HR/L&D、プロダクト責任者。NICE は役割ベースのスキル定義、SAMMは教育とガイダンスの成熟化を支えます。 |
| **具体的な活動フロー** | NICE などで役割を定義し、職種ごとに学習目標を切り分け、開発者には secure coding/PR 判断、PMにはリスク受容、SREにはIR/監視、PSIRTには公開調整を重点化し、最後に演習結果や再発率を次年度計画に反映する循環が望ましいです。 |
| **成果物** | スキルマップ、研修記録、Champion 名簿、演習結果、学習コンテンツ、FAQ、レビュー用 playbook。 |
| **推奨ツール/技術例** | NICE Framework、OWASP SAMM Education & Guidance、Microsoft SDL Training、OWASP DevSecOps Guideline。学習管理システムは何でもよいですが、教材を role-based に分けることが重要です。 |
| **成功指標 KPI 例** | ロール別研修完了率、演習参加率、Security Champion 配置率、同種脆弱性の再発率、設計レビュー参加率、重大インシデント後の学習項目実装率。完了率だけで終わらず、再発率や判断品質を見るべきです。 |
| **よくある課題と対策** | 「年1回のe-learning だけ」で終わると文化にはなりません。対策は、Champion 制度、設計レビューへの常時参加、blameless postmortem、セキュリティ観点を PR/DoD/OKR に埋め込むことです。CISAのSecure by Design が重視するのも、具体的で測定可能な行動変化です。 |

#### その他（明記）として追加を推奨するレイヤー
以下は必須検討項目には含まれていませんが、製品セキュリティ運営では実務上の抜け穴になりやすい領域です。

| その他レイヤー | 追加すべき理由 |
| :--- | :--- |
| **アイデンティティとアクセス管理** | Platform/運用に分散しがちですが、リリース権限、緊急アクセス、CI/CD トークン、顧客データアクセス制御は横断リスクです。BeyondProdや Azure の RBAC/Policy はこの重要性を示しています。 |
| **データ保護とプライバシー** | 脆弱性の severity はデータ種別と露出で大きく変わるため、単独レイヤーとして扱う価値があります。Microsoft SDL も security と privacy を phase ごとに扱います。 |
| **開発者端末と開発環境** | Git 履歴・署名鍵・ローカル secrets が流出すると、アプリやCI/CDの対策を迂回できます。NIST 800-161 が供給の過程全体を対象にするのはこのためです。 |

#### 教育・文化・その他 実務チェックリスト
- [ ] ロール別スキルマップを持っている
- [ ] Champion 制度が名目だけでなくレビュー権限を持つ
- [ ] 研修後に演習や設計レビューで定着確認している
- [ ] 重大障害・脆弱性は教材化している
- [ ] 採用/評価制度にセキュリティ判断を組み込んでいる
- [ ] アイデンティティ/データ保護/端末管理を“他部門任せ”にしていない

---

## 企業事例の比較と統合チェックリスト

### 企業事例の比較表

以下では、Microsoft、Cisco、Panasonicの公開資料を比較し、どのレイヤーに強みがあるかを見ます。ここでの「示唆」は公開情報からの実務的な読み取りです。

| 企業 | 公開資料に見える強み | レイヤー上の特徴 | 実務上の示唆 | 出典 |
| :--- | :--- | :--- | :--- | :--- |
| **Microsoft** | SDL を requirements, design, implementation, verification, release に分け、training と response を supporting activity として置いています。さらに manual code review、automated tooling、credential scanners を commit pipeline に組み込んでいます。 | アプリ、教育、ガバナンスに強い。secure SDLCを“工程”ではなく“強制される標準”として運用している点が特徴です。 | まずアプリ層と教育層をSDLの phase に沿って定義し、その後に CI/CD と監査証跡を追加すると導入しやすい。 | Microsoft Learn / Microsoft SDL 公式資料。 |
| **Cisco** | PSIRT が global team として脆弱性情報の収集・調査・公開を管理し、CVSS と独自SIRを併用し、openVuln API、RSS、定時 disclosure、CVRF/OVAL の機械可読情報提供を行っています。 | PSIRT、対外、公開オペレーションに強い。advisory を単なる文章で終わらせず、配信チャネルと機械可読性まで設計している点が成熟の証拠です。 | PSIRT 成熟度を上げるには、窓口、優先順位の明確化、定期公開 cadence、機械可読配信をセットで整えるべきです。 | Cisco PSIRT ポリシー・日本語サマリ。 |
| **Panasonic** | 全社 bug bounty は実施していない一方、脆弱性情報コーディネーション/開示ポリシーを持ち、2021年から CNAとして CVE ID を割り当て、公開 advisory list を継続提供しています。 | 国内企業として、対外窓口・PSIRT・公開 advisory の実装が分かりやすい。特に「窓口」「ポリシー」「CNA」「公開一覧」の四点セットが揃っています。 | 日本企業では、まずbug bounty の有無よりも、受付窓口、開示方針、CNA/外部識別子、公開一覧を整えるほうが優先度が高いです。 | Panasonic PSIRT 公式ページ。 |

### 統合版の実務チェックリスト

最後に、各レイヤーで分散していた項目を、経営・PM・開発リーダーが月次で確認できる統合版にまとめます。チェックは「ある/ない」だけでなく、担当者と cadence を必ず紐付けてください。NIST CSF 2.0 が示す通り、統制は他機能の中心であるべきで、成果は SSDF・PSIRT・IR・SBOM 運用に断片化させないほうが機能します。

| 領域 | 統合チェック項目 | 主担当 | 推奨 cadence | 推奨 KPI |
| :--- | :--- | :--- | :--- | :--- |
| **ガバナンス** | 製品 risk tier が定義され、例外台帳が期限付きで運用されている | CISO/GRC | 四半期 | 期限切れ例外ゼロ率 |
| **アプリ** | 高リスク変更に脅脅モデリングが実施されている | 開発責任者/AppSec | 変更時 | 高リスク変更の脅威モデル実施率 |
| **アプリ** | PRに SAST・秘密情報検査・依存関係レビューが必須化されている | Dev Lead | 毎PR | マージ前重大検出率 |
| **プラットフォーム** | 正式 release に SBOM が添付されている | Platform Eng | 毎 release | SBOM 添付率 |
| **プラットフォーム** | 重要 artifact に署名と provenance がある | Platform Eng/CloudSec | 毎 release | 署名済み artifact 比率 |
| **プラットフォーム** | CI/CD トークンと runner が最小権限化されている | IAM/Platform | 月次 | 高権限 runner 数 |
| **運用** | 必須ログ一覧と中央 log management がある | SRE/SecOps | 月次 | 重要ログ有効化率 |
| **運用** | 重大インシデント runbook が演習済みである | IR Manager | 四半期 | 演習完了率 |
| **運用** | ポストモーテムが設計/監視改善に戻っている | SRE/Dev Lead | 事象後 | 30日内是正完了率 |
| **対外** | Tier 1 供給者との通知条項・SBOM 条項が整っている | 調達/法務 | 半期 | Tier 1 契約整備率 |
| **対外** | 顧客向け標準証跡パックが更新されている | Security Assurance | 月次 | 質問票回答中央値 |
| **PSIRT** | 受付確認 SLA・severity 別 fix SLAが公開/合意済み | PSIRT | 月次 | 受付確認時間、fix LT |
| **PSIRT** | CVSS だけでなく EPSS/KEV/顧客影響を見て優先順位付けしている | PSIRT/Dev Owner | ケースごと | 重大案件の優先順位妥当率 |
| **PSIRT** | Advisory を公開し、可能なら CSAF/VEXを配布している | PSIRT/Support | 案件ごと | 機械可読 advisory 率 |
| **教育** | ロール別トレーニングと Champion 制度がある | Eng Manager/HR/AppSec | 半期 | Champion 配置率 |
| **教育** | 同種脆弱性の再発が教材化されている | AppSec/PSIRT | 事象後 | 再発率 |

---

## 優先順位のつけ方

- **小規模組織**：まず受付窓口、脅威モデリング対象基準、PRスキャン、SBOM、自社ログ基準、重大脆弱性SLAの**6点**に絞るべきです。
- **中規模組織**：これに署名/provenance、中央 log management、PSIRT case management、標準証跡パックを追加します。
- **大規模組織**：さらにCSAF/VEX、policy-as-code、例外自動期限管理、顧客別影響分析を整えるのが順当です。

これは SSDF、SLSA、PSIRT Maturity、METI SBOM 手引の「段階的導入」と整合する優先順です。

---

## このレポートの結論

プロダクトセキュリティを機能させる鍵は、レイヤーを増やすことではなく、各レイヤーの責任境界・成果物・KPIを明示し、隣接レイヤーとの受け渡しを機械可読化することです。
アプリは「**安全な変更**」、プラットフォームは「**信頼できる供給**」、運用は「**見える化と復旧**」、対外は「**説明責任**」、PSIRTは「**脆弱性ライフサイクル**」、ガバナンスは「**継続可能な優先順位付け**」、教育は「**判断力の再生産**」を担います。これを一枚の運営モデルに統合できた組織ほど、開発速度と安全性の両立に成功しやすいと言えます。

---

### 参考文献・一次情報ソース

1. **SP 800-218**, Secure Software Development Framework (SSDF) Version 1.1: [https://csrc.nist.gov/pubs/sp/800/218/final](https://csrc.nist.gov/pubs/sp/800/218/final)
2. **METI**, 「ソフトウェア管理に向けたSBOM (Software Bill of Materials) の導入に関する手引」: [https://www.meti.go.jp/press/2023/07/20230728004/20230728004.html](https://www.meti.go.jp/press/2023/07/20230728004/20230728004.html)
3. **SP 800-61 Rev. 3**, Incident Response Recommendations and Considerations for Cybersecurity Risk Management: [https://csrc.nist.gov/pubs/sp/800/61/r3/final](https://csrc.nist.gov/pubs/sp/800/61/r3/final)
4. **Microsoft Security Development Lifecycle (SDL)**: [https://learn.microsoft.com/en-us/compliance/assurance/assurance-microsoft-security-development-lifecycle](https://learn.microsoft.com/en-us/compliance/assurance/assurance-microsoft-security-development-lifecycle)
5. **SLSA Security levels**: [https://slsa.dev/spec/v1.0/levels](https://slsa.dev/spec/v1.0/levels)
6. **SP 800-161 Rev. 1**, Cybersecurity Supply Chain Risk Management Practices: [https://csrc.nist.gov/pubs/sp/800/161/r1/final](https://csrc.nist.gov/pubs/sp/800/161/r1/final)
7. **JPCERT/CC**, PSIRT Services Framework と PSIRT Maturity Document: [https://www.jpcert.or.jp/research/psirtSF.html](https://www.jpcert.or.jp/research/psirtSF.html)
8. **The NIST Cybersecurity Framework (CSF) 2.0**: [https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf](https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf)
9. **NICE Framework Resource Center | NIST**: [https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center)
10. **OWASP Application Security Verification Standard**: [https://owasp.org/www-project-application-security-verification-standard/migrated_content](https://owasp.org/www-project-application-security-verification-standard/migrated_content)
11. **Microsoft Security DevOps Azure DevOps extension**: [https://learn.microsoft.com/ja-jp/azure/defender-for-cloud/configure-azure-devops-extension](https://learn.microsoft.com/ja-jp/azure/defender-for-cloud/configure-azure-devops-extension)
12. **GitHub**, 依存関係の確認: [https://docs.github.com/ja/code-security/concepts/supply-chain-security/dependency-review](https://docs.github.com/ja/code-security/concepts/supply-chain-security/dependency-review)
13. **Sigstore**, Quickstart with Cosign: [https://docs.sigstore.dev/quickstart/quickstart-cosign/](https://docs.sigstore.dev/quickstart/quickstart-cosign/)
14. **Google Cloud**, Security log analytics in Google Cloud: [https://docs.cloud.google.com/architecture/security-log-analytics](https://docs.cloud.google.com/architecture/security-log-analytics)
15. **Google Cloud**, Best practices for Cloud Audit Logs: [https://docs.cloud.google.com/logging/docs/audit/best-practices](https://docs.cloud.google.com/logging/docs/audit/best-practices)
16. **AWS**, Detection - AWS Well-Architected Framework: [https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-detection.html](https://docs.aws.amazon.com/wellarchitected/latest/framework/sec-detection.html)
17. **OWASP Dependency-Track**: [https://owasp.org/www-project-dependency-track/](https://owasp.org/www-project-dependency-track/)
18. **CVSS v4.0 Specification Document**: [https://www.first.org/cvss/v4.0/specification-document](https://www.first.org/cvss/v4.0/specification-document)
19. **Cisco**, サポート - セキュリティアドバイザリについての補足: [https://www.cisco.com/c/ja_jp/support/local-security-advisory/security-vulnerability-policy-summary.html](https://www.cisco.com/c/ja_jp/support/local-security-advisory/security-vulnerability-policy-summary.html)
20. **Azure**, security management and monitoring overview: [https://learn.microsoft.com/en-us/azure/security/fundamentals/management-monitoring-overview](https://learn.microsoft.com/en-us/azure/security/fundamentals/management-monitoring-overview)
21. **Cyber Resilience Act**: [https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act](https://digital-strategy.ec.europa.eu/en/policies/cyber-resilience-act)
22. **OWASP DevSecOps Guideline**: [https://owasp.org/www-project-devsecops-guideline/latest/00a-Overview](https://owasp.org/www-project-devsecops-guideline/latest/00a-Overview)
23. **Google Cloud**, BeyondProd: [https://docs.cloud.google.com/docs/security/beyondprod](https://docs.cloud.google.com/docs/security/beyondprod)
24. **パナソニック ホールディングス**, 脆弱性報告フォーム 日本: [https://holdings.panasonic/jp/corporate/product-security/psirt/policy.html](https://holdings.panasonic/jp/corporate/product-security/psirt/policy.html)
