---
name: compose-ui
description: Jetpack Composeでの画面・コンポーネント実装。state hoisting・Preview整備・再コンポーズの規律。
---

# Jetpack Compose UI実装

保守しやすく無駄な再コンポーズのない Compose UI を実装するスキル。

## 実装の原則

1. **ステートレス優先（state hoisting）**: Composable は「状態 + コールバック」を引数で受け、状態を持たない形を基本にする。状態は ViewModel（画面状態）か呼び出し側（UI ローカル状態）へ引き上げる
2. **UiState を受けて描画するだけ**: 画面 Composable は `XxxScreen(uiState, onEvent...)` の形。ViewModel 直参照はルート1箇所だけにし、下層はプレーンな引数で受ける（Preview 可能性とテスト可能性のため)
3. **単方向**: 子から親へはコールバック、親から子へはデータ。子が親の状態を直接書き換えない
4. **既存のデザインシステムに従う**: 色・タイポ・余白は `MaterialTheme` 経由で参照する。リテラル色・dp の直書きが増えたら `material-theming` スキルでトークン化を提案

## 頻出実装の型

**リスト**: `LazyColumn` + **必ず `key` を指定**（並べ替え・削除時の状態ズレとアニメーション崩れを防ぐ）。アイテム Composable は分離してステートレスに。

**フォーム**: 入力値は `rememberSaveable` か ViewModel に（画面回転で消えるバグの定番）。バリデーションは ViewModel 側、表示だけ UI。

**ロード/エラー/空**: UiState の sealed interface か data class のフラグで分岐し、3状態（+コンテンツ）を最初から設計する。後付けすると全画面を触ることになる。

**副作用**: `LaunchedEffect`（キー変化で再実行）/ `DisposableEffect`（後始末）/ `rememberCoroutineScope`（イベント起点）を使い分ける。Composable 本体で直接 I/O・ログ・状態変更をしない（再コンポーズのたびに実行される）。

## 再コンポーズの規律

- 書きながら守る: ラムダの安定化（メソッド参照や `remember` 化）、リストに不変データを渡す、`derivedStateOf` で高頻度変化を間引く
- ただし**計測なしの過剰最適化はしない**。目に見えるカクつきが出たら `android-performance` スキル（Layout Inspector の再コンポーズカウント）で特定してから直す

## 手順

1. 対象画面の仕様（UiState・イベント・遷移）を確認する。デザイン画像があれば読み込んで構造を分解する
2. コンポーネント分割を決める（画面 → セクション → アイテムの3層目安)
3. 実装する。既存プロジェクトの命名・ディレクトリ・テーマ利用の流儀に合わせる
4. **`@Preview` を必ず添える**: 代表状態（通常・ロング文字列・空・エラー）+ ダークテーマ + フォントスケール大。Preview が書けない構造は依存が漏れているサイン
5. 検証: ビルド + 可能なら実機/エミュレータで確認。回転・ダークモード・小さい画面での崩れを見る

## レビュー観点（レビュー依頼の場合)

- Composable 本体での副作用実行、`remember` 忘れによる毎回再生成
- `LazyColumn` の `key` 欠落、リスト内での `ViewModel` 直参照
- 状態の下持ち（子に隠れた `mutableStateOf`）による親子不整合
- ハードコード文字列（`strings.xml` 化）・色・サイズ
- タップ領域 48dp 未満、コンテンツ説明の欠落（詳細は `android-accessibility`)

## 注意事項

- Compose API は進化が速い。非推奨警告（`Divider`→`HorizontalDivider` 等）はビルド出力を確認し、現行 API に合わせる
- View 混在プロジェクトでは `ComposeView`/`AndroidView` の境界を明確にし、無理に一括 Compose 化しない
- アニメーションは要件にあるものだけ。装飾的アニメの提案は動作完成後に
