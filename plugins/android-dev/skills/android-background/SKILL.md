---
name: android-background
description: Androidバックグラウンド処理。Coroutines規律・WorkManager・Foreground Service・Doze対応。
---

# Androidバックグラウンド処理

OSに殺される前提で、確実に動くバックグラウンド処理を設計するスキル。

## 大原則

**Androidはバックグラウンドのプロセスをいつでも殺す。** 「アプリが生きている前提」の設計（GlobalScope、無限ループのスレッド、常駐Serviceへの依存）は必ず壊れる。処理の性質から正しい実行機構を選ぶことがこのスキルの本体。

## 実行機構の選択表

| 処理の性質 | 使うもの |
|---|---|
| 画面が見えている間だけの処理 | ViewModelScope + Coroutines |
| 今すぐ・アプリ終了後も完遂すべき短い処理（送信・保存） | WorkManager (OneTime, expedited) |
| 定期実行（同期・バックアップ） | WorkManager (Periodic, 最短15分) |
| ユーザーが認識する継続処理（再生・ナビ・記録） | Foreground Service（種別宣言必須） |
| サーバー起点の即時処理 | FCM（`android-notifications` 参照） |
| 正確な時刻のアラーム | AlarmManager（正確なアラーム権限に注意） |

「アプリを閉じても続けたい」という要望はまずWorkManagerで満たせないか検討し、Foreground Service は最後の選択肢にする（OS 側の種別制限・審査要件が年々厳しくなるため）。

## 手順

### 1. 要件の分解

処理ごとに: 完遂保証は必要か / 遅延は許されるか / 実行条件（充電中・Wi-Fi）/ 失敗時のリトライ方針 / ユーザーに見せるか — を決めてから機構を選ぶ。

### 2. Coroutines の規律

- **GlobalScope 禁止**。スコープは生存期間に紐付ける（viewModelScope / lifecycleScope / 独自スコープなら明示的キャンセル責任）
- suspend 関数はメインセーフに（スレッド切替は関数の内側 `withContext` で解決）
- キャンセル対応: 長い処理は `ensureActive()` / キャンセル例外を握りつぶさない（`catch (e: Exception)` で CancellationException を食うのは頻出バグ — 再throwする）
- 並行数の制御が必要な処理（画像アップロード等）は Semaphore や Flow の `flatMapMerge(concurrency)` で明示する

### 3. WorkManager の実装要点

- Worker は**冪等に**書く（リトライ・重複実行されても壊れない）。一意性が必要なら `enqueueUniqueWork` + ポリシーを明示
- 制約（ネットワーク・充電）とバックオフを宣言し、`Result.retry()` / `failure()` を正しく使い分ける
- 進捗の UI 反映は WorkInfo の監視で。DBを介す方が堅いことも多い
- **テスト**: WorkManager の TestDriver で制約充足・定期実行を単体テストできる

### 4. Doze・メーカー独自の電池最適化

- Doze / アプリスタンバイで「定期処理は遅れて当然」の前提で設計する（正確性が必要ならその根拠を問い直す）
- 国内含む一部メーカーの積極的なタスクキルは WorkManager でも遅延する。クリティカルな同期は「アプリ起動時の差分同期」を併設して自衛する
- `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` の常用は Play ポリシー上リスク。本当に該当する用途か確認する

### 5. 検証

- [ ] 処理中にアプリをスワイプキル → 完遂 or 再開されるか
- [ ] 機内モード中に実行 → 復帰後にリトライされるか
- [ ] `adb shell cmd deviceidle` で Doze を強制して定期処理の挙動を見る
- [ ] 二重起動（連打・重複スケジュール）で多重実行されないか
- [ ] プロセス再生成後の進捗表示の整合

## 注意事項

- Foreground Service を使う場合、targetSdk 相応の種別（`foregroundServiceType`）宣言と、種別ごとの許可要件を最新ドキュメントで確認する（毎年変わる領域。古い知識で書かない）
- 「即時性」と「確実性」はトレードオフ。両方を求められたら FCM + WorkManager の組み合わせを検討する
- バックグラウンドでの位置情報取得は別格に審査が厳しい。要件の必然性から確認する
