# パーサー (Parsers)

Microsoft Sentinel で使用するカスタムパーサー (KQL 保存済み関数) を管理します。

## Fortigate CEF パーサー

| ファイル | 関数名 | 説明 |
|---------|--------|------|
| `FortigateCEF.kql` | `FortigateCEF` | Fortigate CEF ログの標準フィールドリネームおよび `AdditionalExtensions` からの固有フィールド展開 |

### 展開されるフィールド

#### ネットワーク基本情報

| フィールド | ソース | 説明 |
|-----------|--------|------|
| `SrcIP` / `DstIP` | 標準 CEF | 送信元 / 宛先 IP |
| `SrcPort` / `DstPort` | 標準 CEF | 送信元 / 宛先ポート |
| `SrcZone` / `DstZone` | `FTNTFGTsrcintfrole` | ゾーン (trust / untrust 等) |
| `SrcInterface` / `DstInterface` | `FTNTFGTsrcintf` | 物理/論理インターフェース |
| `SrcCountry` / `DstCountry` | `FTNTFGTsrccountry` | GeoIP 国名 |
| `Protocol` | 標準 CEF | プロトコル |
| `Direction` | 標準 CEF | 通信方向 |

#### ポリシー / セッション

| フィールド | ソース | 説明 |
|-----------|--------|------|
| `PolicyID` / `PolicyName` | `FTNTFGTpolicyid` | ファイアウォールポリシー |
| `SessionID` | `FTNTFGTsessionid` | セッション ID |
| `Duration` | `FTNTFGTduration` | セッション継続時間 (秒) |
| `BytesSent` / `BytesReceived` | 標準 CEF | 送受信バイト数 |

#### UTM (IPS / AV / Web Filter / App Control)

| フィールド | ソース | 説明 |
|-----------|--------|------|
| `AttackName` / `AttackID` | `FTNTFGTattack` | IPS 検出シグネチャ |
| `VirusName` | `FTNTFGTvirus` | AV 検出ウイルス名 |
| `WebCategory` / `WebDomain` | `FTNTFGTcatdesc` | Web フィルタ カテゴリ / ドメイン |
| `AppCategory` / `AppName` | `FTNTFGTappcat` | アプリケーション制御 |

#### VPN / NAT / その他

| フィールド | ソース | 説明 |
|-----------|--------|------|
| `VPNTunnel` / `VPNType` | `FTNTFGTtunnelip` | VPN トンネル情報 |
| `NatSrcIP` / `NatDstIP` | `FTNTFGTtransip` | NAT 変換後 IP |
| `VDOM` | `FTNTFGTvd` | 仮想ドメイン |
| `SrcUserGroup` | `FTNTFGTgroup` | ユーザーグループ |

### Log Analytics への登録方法

#### Azure Portal

1. **Log Analytics ワークスペース** > **ログ** を開く
2. クエリエディタに `FortigateCEF.kql` の内容を貼り付けて実行確認
3. **保存** > **関数として保存** を選択
4. 以下を入力:
   - **名前**: `FortigateCEF`
   - **カテゴリ**: `Parsers`
5. **保存** をクリック

#### Azure CLI

```bash
az monitor log-analytics workspace saved-search create \
  --resource-group <resource-group> \
  --workspace-name <workspace-name> \
  --name "FortigateCEF" \
  --category "Parsers" \
  --display-name "FortigateCEF" \
  --saved-query "$(cat FortigateCEF.kql)" \
  --function-alias "FortigateCEF"
```

### 使用例

```kusto
// 基本的な呼び出し
FortigateCEF
| take 100

// 拒否トラフィックの国別集計
FortigateCEF
| where Action == "deny"
| summarize Count = count() by SrcCountry
| top 10 by Count

// IPS 検出イベント
FortigateCEF
| where isnotempty(AttackName)
| project EventTime, SrcIP, DstIP, DstPort, AttackName, Action

// 特定ゾーン間の通信
FortigateCEF
| where SrcZone == "untrust" and DstZone == "trust"
| where Action == "accept"
| summarize ConnectionCount = count() by SrcIP, DstIP, DstPort
| top 20 by ConnectionCount
```
