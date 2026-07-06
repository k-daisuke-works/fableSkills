# Game Dev Skills

ゲーム開発のライフサイクル全体をカバーする Claude Code スキル集。

## インストール

```
/plugin marketplace add k-daisuke-works/fableSkills
/plugin install game-dev@fable-skills
```

ローカルで試す場合:

```
/plugin marketplace add /path/to/fableSkills
/plugin install game-dev@fable-skills
```

インストール後は `/game-dev:スキル名` で呼び出せます（例: `/game-dev:game-prototype`）。文脈に合えば Claude が自動で使うこともあります。

## スキル一覧

### 企画・設計

| スキル | 用途 |
|---|---|
| `game-concept` | 企画書・コンセプトシートの作成。コアループの1文化とスコープ検証 |
| `game-design-doc` | GDD の作成・更新・レビュー。曖昧語を数値仕様に落とす |
| `level-design` | ステージ設計と難易度カーブの設計・レビュー |
| `game-narrative` | 世界観・キャラ・シナリオ・ゲーム内テキストの設計と執筆 |

### 実装

| スキル | 用途 |
|---|---|
| `game-prototype` | コアループ検証用の遊べるプロトタイプを最速で作る |
| `game-loop-review` | フレーム予算・GC・描画負荷の計測ベースのパフォーマンスレビュー |
| `placeholder-assets` | SVGスプライト・効果音・パーティクル等の仮素材生成 |

### 調整

| スキル | 用途 |
|---|---|
| `game-balance` | 数値バランスの設計とシミュレーションによる検証 |
| `game-economy` | 通貨・報酬・ドロップ率・ガチャ確率の設計と健全性検証 |
| `game-feel` | 手触り・ジュース（入力応答・ヒット感）のレビューと改善実装 |

### QA・リリース

| スキル | 用途 |
|---|---|
| `playtest-plan` | プレイテスト計画・QA観点表の作成とゲーム特有バグの探索 |
| `game-localization` | 多言語対応の設計・実装・レビュー（テキスト外部化・レイアウト検証） |
| `game-release-checklist` | リリース前チェックとプラットフォーム別公開準備 |

## エージェント一覧

役割を分離した6体構成。実装と検証を別エージェントに分けるのが運用の核です。

| エージェント | 役割 | やらないこと |
|---|---|---|
| `game-designer` | 仕様判断・GDDの更新と整合性チェック（仕様の番人） | 実装 |
| `gameplay-programmer` | 仕様が固まった機能の実装（1機能ずつ） | 仕様の勝手な変更 |
| `qa-playtester` | 敵対的テスト。進行不能・状態不整合を再現手順付きで報告 | 修正 |
| `balance-simulator` | 数値バランスのシミュレーション検証と調整案 | 本体コードの変更 |
| `perf-profiler` | 計測ベースのパフォーマンス調査・修正 | 計測なしの書き換え |
| `asset-artist` | プレースホルダー素材の一括生成と組み込み | 本番素材の上書き |

## 付属テンプレート

| ファイル | 用途 |
|---|---|
| [templates/game-requirements.html](templates/game-requirements.html) | ブラウザで開いて埋めるだけで、AIに渡してゲームを完成させられる要件定義書（Markdown）が生成されるフォーム。自動保存・記入例・必須チェック付き |
| [templates/game-project-CLAUDE.md](templates/game-project-CLAUDE.md) | ゲームプロジェクト用 CLAUDE.md テンプレート。プロジェクトルートに `CLAUDE.md` としてコピーして `<>` を埋める。仕様の優先順位・開発ルール・エージェントの使い分けを定義済み |

## 想定ワークフロー

```
game-concept → game-design-doc → game-prototype → game-feel
                    ↓                                  ↓
              level-design / game-narrative      game-balance / game-economy
                    ↓                                  ↓
              playtest-plan → game-localization → game-release-checklist
```

各スキルは単独でも使えます。エンジン非依存（Unity / Godot / Web / Pygame 等、プロジェクトの構成に合わせて動作します）。
