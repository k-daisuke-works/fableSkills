---
name: android-data
description: Androidデータ層の実装。Room・DataStore・API通信・Repository・オフラインファースト設計。
---

# Androidデータ層

信頼できる唯一のデータ源（Single Source of Truth）を持つデータ層を実装するスキル。

## 大原則

**UIはデータの出どころを知らない。** 画面は Repository が公開する Flow を購読するだけで、それが DB 由来かネット由来かを知らない構造にする。これが守られていれば、キャッシュ戦略・API変更・オフライン対応をデータ層の中だけで完結できる。

## 技術選定の型

| 用途 | 選択 | 備考 |
|---|---|---|
| 構造化データの永続化 | Room | Flow 対応。マイグレーション必須(後述) |
| 設定・フラグ・小さなKV | DataStore (Preferences) | SharedPreferences は新規で使わない |
| API 通信 | Retrofit + kotlinx.serialization / Ktor Client | 既存プロジェクトの流儀優先 |
| 画像 | Coil (Compose 対応) | |
| 機密の保存 | Keystore ベースの暗号化 | トークンを平文 DataStore に置かない |

バージョンは Version Catalog に集約し、最新安定版を確認して使う。

## 手順

### 1. データ設計

- 扱うデータの一覧と「真実源はどこか」（サーバー / ローカル / 両方）を決める
- ネットワークデータを画面に出すアプリは、**オフラインファースト**を基本形として検討する: Room を真実源にし、API は Room を更新する同期役。UI は Room の Flow だけを見る
- DTO（API の形）と Entity（DB の形）とドメインモデル（アプリの形）を混ぜない。小規模なら DTO=ドメインの簡略も可だが、変換関数の置き場だけは決めておく

### 2. 実装の要点

**Room**
- Entity・DAO・Database を定義。DAO は `Flow<List<T>>` を返し、変更が UI に自動反映される形に
- **スキーマ変更時のマイグレーションを最初から運用に組み込む**（`exportSchema = true` でスキーマ履歴を git 管理、変更時は Migration + テスト。`fallbackToDestructiveMigration` はリリース後のアプリでは事故）

**API 通信**
- レスポンスは成功/失敗を型で表す（`Result<T>` や sealed class）。例外を UI 層まで素通りさせない
- エラー分類を最初に設計する: ネットワーク不通 / 4xx（再試行無意味）/ 5xx・タイムアウト（再試行可能）→ UI に出すメッセージと再試行ボタンの有無が変わる
- 認証トークンの付与は Interceptor に集約。リフレッシュ処理は同時多発リクエストでの競合（複数回リフレッシュ）を考慮する

**Repository**
- コンストラクタ注入（`android-architecture` の DI 方針に従う）
- スレッド境界は Repository までで解決し（`withContext(ioDispatcher)`）、ViewModel に Dispatchers を意識させない。Dispatcher は注入してテスト差し替え可能に

### 3. 検証

- Room: マイグレーションテスト、DAO の単体テスト（インメモリDB）
- API: モックサーバー（MockWebServer 等）で成功・各エラー・タイムアウトのハンドリングをテスト
- オフライン動作: 機内モードでの起動・閲覧・（要件なら）書き込みキューイングを実機確認
- プロセス再生成後にデータが正しく復元されるか

## 注意事項

- 同期ロジック（いつ API を叩くか: 起動時 / 画面表示時 / pull-to-refresh / 定期）は要件から明示的に決め、コードにコメントで方針を残す。バックグラウンド定期同期は `android-background`（WorkManager）へ
- ページングが必要な一覧は Paging ライブラリの採用をコストとともに提案する（自作ページングは端が壊れやすい）
- 個人情報・トークンをログに出さない。Retrofit のログインターセプタはリリースビルドで無効化する
