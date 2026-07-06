---
name: android-scaffold
description: Android新規プロジェクトの雛形生成（Kotlin+Compose+Version Catalog）。ビルドが通る状態まで作る。
---

# Androidプロジェクト雛形生成

ビルドが通り、そのまま機能開発を始められるモダンなAndroidプロジェクトを生成するスキル。

## 大原則

**バージョンをハードコードした知識で書かない。** AGP・Kotlin・Compose BOM・各ライブラリの安定版は数ヶ月で変わる。生成前に最新安定版を確認し（WebSearch で「Android Gradle Plugin releases」「Compose BOM release」等、または既存プロジェクトの値）、`gradle/libs.versions.toml`（Version Catalog）に集約する。

## 手順

### 1. 前提の確認

- アプリの内容と主要画面（要件があれば読む）
- minSdk の希望（指定がなければ 26 前後を提案。理由: 実質カバー率と保守コストのバランス。targetSdk は最新安定版 — Play 提出には毎年の引き上げ要件があるため必ず最新を確認）
- 環境: JDK・Android SDK・Android Studio の有無を確認（`java -version`, `sdkmanager` の存在）。CLI ビルドできる環境かで検証手段が変わる

### 2. 標準構成

```
app/
├── build.gradle.kts
└── src/main/
    ├── AndroidManifest.xml
    └── java/<パッケージ>/
        ├── MainActivity.kt
        ├── MyApplication.kt        # Hilt使用時
        ├── ui/
        │   ├── theme/              # Color.kt / Theme.kt / Type.kt
        │   └── <feature>/          # 画面ごと: XxxScreen.kt / XxxViewModel.kt
        ├── data/                   # repository / local / remote
        └── di/                     # Hiltモジュール
gradle/libs.versions.toml           # Version Catalog（依存は必ずここ経由）
build.gradle.kts / settings.gradle.kts
```

技術選定のデフォルト（ユーザー指定があればそれを優先）:

- UI: **Jetpack Compose + Material 3**（View/XML は既存プロジェクトの流儀がある場合のみ）
- 言語・ビルド: Kotlin / Gradle Kotlin DSL / Version Catalog
- DI: Hilt（小規模なら手動DIでも可と提案）
- 非同期: Coroutines + Flow
- 画面遷移: Navigation Compose

規模に応じて過剰装備にしない。単画面ツールに Hilt+マルチモジュールは不要 — 「今の規模の一段上」まで。

### 3. 生成と検証

1. 全ファイルを生成する（Manifest には必要最小限の宣言のみ。不要な権限を入れない）
2. `.gitignore`（`build/`, `local.properties`, `*.keystore` 等）を必ず含める
3. **ビルドで検証する**: `./gradlew assembleDebug`。Gradle Wrapper がない場合は生成方法を案内。SDK がない環境なら、その旨と Android Studio での開き方を伝える
4. エミュレータ/実機が使えるなら起動確認まで（`adb devices` で確認）

### 4. 引き渡し

- 起動方法（Android Studio で開く / `./gradlew installDebug`）
- どこに何を書くかの案内（画面追加は `ui/<feature>/`、依存追加は Version Catalog）
- 次のステップ: 設計は `android-architecture`、画面実装は `compose-ui` スキルへ

## 注意事項

- パッケージ名はユーザーに確認する（後からの変更は面倒）。逆ドメイン形式（`com.example.app` はストア公開不可なので実名を推奨）
- Compose BOM を使い、Compose 個別ライブラリのバージョン直書きをしない
- テンプレコードに TODO を残す場合は必ず理由と次の作業を書く（無言の TODO を残さない）
