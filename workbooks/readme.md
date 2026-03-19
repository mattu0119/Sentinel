# ワークブック (Workbooks)

Microsoft Sentinel に展開する可視化ダッシュボード (Azure Workbook) を管理します。

## ワークブック一覧

| ファイル | 説明 | データソース |
|---------|------|-------------|
| `Workbook-FortigateCEF.json` | Fortigate CEF ログ ダッシュボード | `CommonSecurityLog` |

## Workbook-FortigateCEF.json

Fortigate ファイアウォールの CEF ログを多角的に可視化するダッシュボードです。

### パラメータ

| パラメータ | 説明 |
|-----------|------|
| 時間範囲 | 1 時間 〜 30 日 (カスタム可) |
| ファイアウォール | `DeviceName` でフィルタ (全選択可) |

### セクション構成

| セクション | 含まれるパネル |
|-----------|--------------|
| **概要** | サマリータイル (総イベント数、許可/拒否数、ユニーク IP 数、転送量)、アクション別イベント推移、トラフィック量推移 |
| **トップトーカー** | 送信元 IP トップ 20、宛先 IP トップ 20、宛先ポート トップ 20、アプリケーション トップ 20 |
| **拒否トラフィック分析** | 拒否イベント推移、拒否送信元 IP トップ 20、ポート別拒否分布 |
| **UTM セキュリティイベント** | IPS イベント推移 (重大度別)、IPS シグネチャ トップ 30、AV 検出ウイルス トップ 20、Web フィルタ ブロックカテゴリ |
| **地理情報** | 送信元国別マップ、宛先国別マップ |
| **VPN** | VPN イベント推移、VPN 認証失敗 直近 50 件 |
| **ポリシー使用状況** | ポリシー別ヒット数トップ 50 |

### 展開方法

#### Azure Portal (手動インポート)

1. **Microsoft Sentinel** > **ブック** > **マイ ブック** を開く
2. **+ 追加** をクリック
3. **詳細エディター** (`</>`) を開く
4. `Workbook-FortigateCEF.json` の内容を貼り付け
5. **適用** > **保存** をクリック

#### Azure CLI

```bash
az portal dashboard import \
  --resource-group <resource-group> \
  --input-path Workbook-FortigateCEF.json \
  --name "Fortigate CEF Dashboard"
```

#### Sentinel Repositories (CI/CD)

リポジトリ接続を構成済みであれば、`workbooks/` フォルダへのプッシュで自動展開されます。

### 前提条件

- Fortigate CEF ログが `CommonSecurityLog` テーブルに取り込まれていること
- `AdditionalExtensions` に `FTNTFGT*` フィールドが含まれていること (地理情報・ポリシー・UTM パネルで使用)
