# fableSkills

Claude Code 用のカスタムスキル・プラグイン集。プラグインマーケットプレイスとして機能し、他のマシン・プロジェクトからインストールして使えます。

## リポジトリ構成

```
.claude-plugin/marketplace.json   # マーケットプレイス定義
plugins/
└── game-dev/                     # ゲーム開発スキル集（13スキル）
    ├── .claude-plugin/plugin.json
    ├── README.md                 # スキル一覧と使い方
    └── skills/
.claude/skills/                   # このリポジトリ内でのみ使うプロジェクトスキル
└── iso27001-review/
```

## プラグインのインストール

任意のマシン・プロジェクトの Claude Code で:

```
/plugin marketplace add k-daisuke-works/fableSkills
/plugin install game-dev@fable-skills
```

ローカルパスから試す場合:

```
/plugin marketplace add /path/to/fableSkills
/plugin install game-dev@fable-skills
```

更新を配信するには、このリポジトリにコミット & プッシュするだけです（利用側は `/plugin marketplace update fable-skills`）。

## 収録プラグイン

| プラグイン | 内容 |
|---|---|
| [game-dev](plugins/game-dev/README.md) | ゲーム開発のライフサイクル全体をカバーする13スキル + 6エージェント + テンプレート（要件定義HTML・CLAUDE.md） |
| [marketing-biz](plugins/marketing-biz/README.md) | マーケティング9スキル（調査・戦略・コピー・SEO・SNS・PR・ローンチ・KPI）+ ビジネスプランニング6スキル（リーンキャンバス・事業計画・財務モデル・価格・ピッチ・OKR） |
| [android-dev](plugins/android-dev/README.md) | Androidアプリ開発の全工程（雛形・設計・Compose UI・データ層・バックグラウンド・品質・リリース・CI）をカバーする15スキル + 5エージェント |

## スキル設計の方針

- **description は常時コンテキストに載る**（本文は呼び出し時のみ読まれる）ため、description は1〜2文に圧縮し、詳細な誘導は本文に書く
- 手順・チェックリストは SKILL.md 本文に、長大な参照資料は `references/` に分離して段階的に読み込ませる

## プロジェクトスキル（このリポジトリ専用）

| スキル | 説明 |
|---|---|
| [iso27001-review](.claude/skills/iso27001-review/SKILL.md) | コード変更・リポジトリを ISO/IEC 27001:2022 附属書A の管理策に照らしてレビューし、管理策番号付きで指摘を報告する |
