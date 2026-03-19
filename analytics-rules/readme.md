# 分析ルール (Analytics Rules)

Microsoft Sentinel に展開する分析ルールの ARM テンプレートを管理します。

## 命名規則

`<severity>-<data-source>-<description>.json`

## Fortigate CEF ルール

Fortigate の CEF ログ (`CommonSecurityLog`) から不審な通信を検出するルールです。

| ファイル | 重大度 | 検出内容 | 実行間隔 | MITRE ATT&CK |
|---------|--------|---------|---------|--------------|
| `high-fortigate-cef-brute-force-login.json` | High | VPN/管理画面へのブルートフォース攻撃 (1h 内に認証失敗 10 回以上) | 1 時間 | T1110 |
| `high-fortigate-cef-outbound-to-rare-port.json` | High | 一般的でないポートへのアウトバウンド通信 (C2 通信の疑い) | 1 時間 | T1571 |
| `high-fortigate-cef-ips-critical-alert.json` | High | IPS が重大な攻撃シグネチャを検出 | 5 分 | T1190, T1203 |
| `medium-fortigate-cef-data-exfiltration.json` | Medium | 外部への大量データ送信 (100 MB 超/時間) | 1 時間 | T1048 |
| `medium-fortigate-cef-denied-traffic-spike.json` | Medium | 単一 IP からの拒否トラフィック急増 (ポートスキャン/偵察) | 1 時間 | T1046, T1595 |

## 展開方法

### Sentinel Repositories (推奨)

Sentinel の **リポジトリ接続** を構成すると、Git プッシュ時に自動展開されます。

### 手動展開 (Azure CLI)

```bash
az deployment group create \
  --resource-group <resource-group> \
  --template-file <rule-file>.json \
  --parameters workspace=<workspace-resource-id>
```

### 手動展開 (PowerShell)

```powershell
New-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group> `
  -TemplateFile <rule-file>.json `
  -workspace <workspace-resource-id>
```

## 閾値の調整

環境に応じて以下の値を調整してください。

| ルール | パラメータ | 既定値 | 説明 |
|--------|----------|--------|------|
| brute-force-login | `FailedAttempts` | 10 回 | 認証失敗の閾値 |
| data-exfiltration | `ThresholdBytes` | 100 MB | 送信データ量の閾値 |
| denied-traffic-spike | `DeniedCount` | 100 回 | 拒否トラフィックの閾値 |
| outbound-to-rare-port | `CommonPorts` | 20 ポート | 除外する既知ポートのリスト |
