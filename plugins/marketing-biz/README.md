# Marketing & Business Planning Skills

マーケティングとビジネスプランニングをカバーする Claude Code スキル集（15スキル）。

## インストール

```
/plugin marketplace add k-daisuke-works/fableSkills
/plugin install marketing-biz@fable-skills
```

インストール後は `/marketing-biz:スキル名` で呼び出せます（例: `/marketing-biz:lean-canvas`）。文脈に合えば Claude が自動で使うこともあります。

## スキル一覧

### マーケティング — 調査・戦略

| スキル | 用途 |
|---|---|
| `market-research` | 市場規模推定（TAM/SAM/SOM）・競合分析・3C/5F。出典と推定過程を明示 |
| `persona-journey` | ペルソナとカスタマージャーニーマップ。仮説と実データを区別して設計 |
| `marketing-strategy` | STP・チャネル選定・施策ロードマップ。「やらないこと」を決める戦略設計 |

### マーケティング — 実行

| スキル | 用途 |
|---|---|
| `copywriting` | 訴求軸設計からのコピー作成。方向性の違う複数案+景表法/薬機法チェック |
| `content-seo` | 検索意図ベースの記事設計・執筆・リライト。トピッククラスタ戦略 |
| `sns-plan` | プラットフォーム選定・コンテンツの柱・投稿カレンダー・投稿作成 |
| `press-release` | ニュース価値の診断からのリリース作成と配信計画 |
| `launch-plan` | プレ/当日/ポストの3フェーズ逆算ローンチ計画 |
| `growth-metrics` | NSM・KPIツリー・ファネル(AARRR)設計とボトルネック分析 |

### ビジネスプランニング

| スキル | 用途 |
|---|---|
| `lean-canvas` | リーンキャンバス作成と最重要仮説の検証実験設計 |
| `business-plan` | 読み手（銀行/投資家/社内）別の事業計画書作成。数字の整合性検証が核 |
| `financial-model` | LTV/CAC・3〜5年PL・損益分岐をスクリプトで計算。シナリオ・感度分析付き |
| `pricing-strategy` | コスト/競合/価値の3基準点からの価格・プラン設計と影響試算 |
| `pitch-deck` | ストーリー設計・スライド構成・想定Q&A。整合性チェック付き |
| `okr-planning` | OKR設計とアンチパターン検査、チェックイン運用の設計 |

## 想定ワークフロー

**新規事業の立ち上げ**:
```
lean-canvas（仮説整理・検証） → market-research（裏付け） → financial-model（成立性）
      → pricing-strategy → business-plan / pitch-deck（資金調達）
```

**プロダクトのグロース**:
```
persona-journey → marketing-strategy → 実行系（copywriting / content-seo / sns-plan / press-release）
      → launch-plan（発表） → growth-metrics（計測・改善） → okr-planning(目標運用)
```

各スキルは単独でも使えます。スキル同士は成果物（`docs/` 配下の文書）を介して連携します。
