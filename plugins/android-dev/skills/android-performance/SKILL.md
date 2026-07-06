---
name: android-performance
description: Androidの性能改善。起動・ジャンク・メモリ・サイズ・電池を計測ベースで特定・修正。
---

# Androidパフォーマンス改善

計測で原因を特定し、効果を数値で示せる改善だけを行うスキル。

## 大原則

**計測なしに最適化しない。** 「遅そうなコード」の書き換えは効果がない上に複雑さを足す。必ず「症状の分類 → 計測 → 特定 → 修正 → 再計測」の順で進め、修正前後の数値を報告する。リリースビルド（R8適用）で計測する — debugビルドの数値は別物。

## 症状別の計測と定石

### 起動が遅い

- 計測: `adb shell am start -W`（TTID）、Macrobenchmark（あれば）、Perfetto トレース
- 定石: Application/最初の画面での同期I/O・SDK初期化の洗い出し（遅延初期化・App Startup への移行）/ スプラッシュの過剰な待機 / **Baseline Profile の導入**（起動の定番改善。導入状況を確認して提案）

### スクロール・アニメがカクつく（ジャンク）

- 計測: Perfetto でフレーム時間、Layout Inspector の**再コンポーズカウント**、JankStats
- Compose の定石: リストアイテムの `key` 欠落 / 不安定な引数による全アイテム再コンポーズ / アイテム内での重い計算（`remember` 化・事前計算）/ 高頻度状態変化の `derivedStateOf` 間引き / `Modifier` チェーンでの毎フレームアロケーション
- 共通の定石: メインスレッドでの I/O・重い変換（StrictMode で検出）/ 画像のフルサイズデコード（Coil のサイズ指定）/ 過深なレイアウト階層

### メモリリーク・OOM

- 計測: LeakCanary（debug 導入を提案）、Memory Profiler のヒープダンプ
- 定番の犯人: ViewModel/シングルトンからの Activity・View 参照 / 解除されないリスナー・コールバック / Coroutines の生存期間ミス（`android-background` の規律）/ 大画像のキャッシュ肥大

### アプリサイズが大きい

- 計測: `./gradlew :app:analyzeReleaseBundle` / APK Analyzer で内訳を出す
- 定石: R8（minify+shrinkResources）の有効化確認 / 未使用リソース・巨大画像（WebP化）/ ネイティブライブラリの ABI 分割（AAB なら自動）/ 使っていない依存の削除

### 電池消費

- 計測: Battery Historian / `adb shell dumpsys batterystats`
- 定石: 過頻度の位置情報・センサー / WakeLock の解放漏れ / 過剰な定期同期（`android-background` の設計見直し）

## 手順

1. 症状の聞き取りと再現条件の特定（どの画面・操作・端末で）。低スペック端末での再現確認を重視する
2. 上記の対応する計測を実施（環境がなければ計測コードの仕込みと取得手順をユーザーに渡す）
3. 最大要因から1つずつ修正 → 再計測。**まとめて直さない**（どれが効いたか分からなくなる）
4. 報告: 症状 / 原因（証拠付き）/ 修正内容（`file:line`）/ 前後の数値 / 残る課題

## 継続的な監視（提案）

- リリース前の Macrobenchmark を CI に組む（`android-ci`）
- 本番の計測: Play Console の Android Vitals（起動時間・ジャンク率・ANR）を定点観測する運用を提案

## 注意事項

- 最適化がロジックの挙動を変えていないことを必ず確認する（キャッシュ導入による古いデータ表示などは不具合）
- 可読性を壊すマイクロ最適化は、計測で大きな効果が証明された場合のみコメント付きで
- 端末差が大きい領域。手元のハイエンドで「直った」と判断しない
