# Android Dev Skills

Androidアプリ開発の全工程をカバーする Claude Code スキル集（15スキル）。
Kotlin + Jetpack Compose + Material 3 のモダン構成を前提に、バージョン・Play要件など変化の速い情報は「最新を確認してから書く」方針を各スキルに組み込んでいます。

## インストール

```
/plugin marketplace add k-daisuke-works/fableSkills
/plugin install android-dev@fable-skills
```

インストール後は `/android-dev:スキル名` で呼び出せます（例: `/android-dev:android-scaffold`）。文脈に合えば Claude が自動で使うこともあります。

## スキル一覧

### 立ち上げ・設計

| スキル | 用途 |
|---|---|
| `android-scaffold` | 新規プロジェクト雛形（Compose / Kotlin DSL / Version Catalog）。ビルドが通る状態まで |
| `android-architecture` | UDF・レイヤー分離・DI・モジュール分割。規模に合った複雑さで設計 |

### UI

| スキル | 用途 |
|---|---|
| `compose-ui` | 画面・コンポーネント実装。state hoisting・Preview整備・再コンポーズの規律 |
| `material-theming` | Material 3テーマ。ライト/ダーク同時設計・コントラスト検証・トークン一元化 |
| `android-navigation` | 遷移図ファーストの画面遷移。バックスタック・ディープリンク・予測型バック |

### データ・処理

| スキル | 用途 |
|---|---|
| `android-data` | Room / DataStore / API通信 / Repositoryとオフラインファースト設計 |
| `android-background` | Coroutines規律・WorkManager・Doze対応。「OSに殺される前提」の設計 |
| `android-notifications` | 通知チャンネル設計・13+の権限フロー・FCM・タップ遷移 |
| `android-permissions` | 権限を消せる代替API優先。拒否4状態のフロー実装とデータセーフティ整合 |

### 品質

| スキル | 用途 |
|---|---|
| `android-testing` | ViewModel/Room/API/Compose UIの層別テストとflaky対策 |
| `android-performance` | 起動・ジャンク・メモリ・サイズ・電池。計測→特定→前後比較の規律 |
| `android-accessibility` | TalkBackでタスク完了できる対応。セマンティクス・48dp・フォントスケール |
| `android-debug` | ビルドエラー・クラッシュ・ANRの系統的調査。「Caused byを最深まで読む」 |

### リリース・運用

| スキル | 用途 |
|---|---|
| `android-release` | 署名・R8・AAB・ストア掲載・段階的公開・審査リジェクト対応 |
| `android-ci` | PR検査10分以内のCI設計とリリース自動化（GitHub Actions等） |

## エージェント一覧

役割分離の5体構成。実装と検証を別エージェントに分けるのが運用の核です。

| エージェント | 役割 | やらないこと |
|---|---|---|
| `android-architect` | 設計判断・構造レビュー・ADR記録 | 実装 |
| `android-developer` | 機能実装（1機能ずつ、ビルド・動作確認まで） | 仕様の勝手な変更 |
| `android-qa-tester` | Android特有の破壊テスト（回転・プロセス再生成・権限4状態等） | 修正 |
| `android-perf-engineer` | 計測ベースの性能調査・修正 | 計測なしの書き換え |
| `android-release-engineer` | 署名・R8・CI/CD・Play提出・審査対応 | アプリロジックの変更 |

## 想定ワークフロー

```
android-scaffold → android-architecture → compose-ui / material-theming / android-navigation
       ↓
android-data / android-background / android-notifications / android-permissions
       ↓
android-testing / android-accessibility →（問題発生時: android-debug / android-performance）
       ↓
android-ci → android-release
```

各スキルは単独でも使えます。既存プロジェクトへの適用（レビュー・改善モード）にも対応しています。
