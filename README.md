# Sentinel コンテンツ管理リポジトリ

このリポジトリは Microsoft Sentinel に展開するコンテンツを管理するリポジトリです。

## 管理対象コンテンツ

| カテゴリ | 説明 | 格納先 |
|---------|------|--------|
| **分析ルール (Analytics Rules)** | 脅威検出のためのスケジュール / NRT / Fusion ルール | `analytics-rules/` |
| **自動化ルール (Automation Rules)** | インシデント発生時の自動化ワークフロー定義 | `automation-rules/` |
| **プレイブック (Playbooks)** | Logic Apps ベースのインシデント対応自動化 | `playbooks/` |
| **ブック (Workbooks)** | 可視化・レポート用ダッシュボード | `workbooks/` |
| **ハンティングクエリ (Hunting Queries)** | プロアクティブな脅威ハンティング用 KQL クエリ | `hunting-queries/` |
| **パーサー (Parsers)** | ASIM パーサー / カスタム関数 | `parsers/` |
| **データコネクタ (Data Connectors)** | カスタムデータコネクタ定義 | `data-connectors/` |
| **ウォッチリスト (Watchlists)** | 脅威インテリジェンスや許可リスト等の参照データ | `watchlists/` |
| **データ収集ルール (Data Collection Rules)** | DCR 定義 (CEF / Syslog / カスタムログ) | `data-collection-rules/` |
| **ノートブック (Notebooks)** | Jupyter Notebook による調査・分析 | `notebooks/` |

## ディレクトリ構成

```
Sentinel/
├── README.md                    # 本ファイル
├── analytics-rules/             # 分析ルール (.json / .yaml)
├── automation-rules/            # 自動化ルール
├── playbooks/                   # プレイブック (ARM / Bicep テンプレート)
├── workbooks/                   # ブック (JSON テンプレート)
├── hunting-queries/             # ハンティングクエリ (.kql)
├── parsers/                     # パーサー / ASIM 関数 (.kql)
├── data-connectors/             # データコネクタ定義
├── watchlists/                  # ウォッチリスト (.csv)
├── data-collection-rules/       # データ収集ルール (DCR)
└── notebooks/                   # Jupyter Notebook
```

## コンテンツのエクスポート・インポート

### エクスポート方法
- **Azure Portal**: Sentinel の各ブレードからテンプレートとしてエクスポート
- **Sentinel API**: REST API 経由での一括エクスポート
- **PowerShell**: `Az.SecurityInsights` モジュールを使用
- **Sentinel Repositories**: CI/CD 連携によるソース管理

### ファイル形式
| コンテンツ | 形式 |
|-----------|------|
| 分析ルール | JSON (ARM) / YAML |
| プレイブック | JSON (ARM) / Bicep |
| ブック | JSON |
| ハンティングクエリ | KQL (.kql) |
| パーサー | KQL (.kql) |
| ウォッチリスト | CSV (.csv) |
| データ収集ルール | JSON |

## 命名規則

- 分析ルール: `<severity>-<data-source>-<description>.json`
- ハンティングクエリ: `<data-source>-<description>.kql`
- プレイブック: `Playbook-<action>-<target>/`
- ブック: `Workbook-<description>.json`
- ウォッチリスト: `<description>.csv`