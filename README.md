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

## プロジェクトスキル（このリポジトリ専用）

| スキル | 説明 |
|---|---|
| [iso27001-review](.claude/skills/iso27001-review/SKILL.md) | コード変更・リポジトリを ISO/IEC 27001:2022 附属書A の管理策に照らしてレビューし、管理策番号付きで指摘を報告する |
