# コーディング規則・品質基準

調査日時: 2025-01-17  
調査対象: `/home/penne/dev/projects/YATA/YATA`

## 概要

このドキュメントは、YATAプロジェクトにおけるコーディング規則、品質基準、およびベストプラクティスを包括的に定義します。

## 技術スタック

### 主要依存関係

- **Flutter**: UI フレームワーク
- **flutter_riverpod**: 状態管理
- **supabase_flutter**: バックエンド（PostgreSQL）
- **json_annotation/json_serializable**: JSON シリアライゼーション
- **uuid**: UUID生成
- **decimal**: 高精度数値計算

### 開発依存関係

- **flutter_lints** + **very_good_analysis**: リント設定
- **build_runner**: コード生成
- **flutter_test**: テストフレームワーク

## アーキテクチャ

### 採用アーキテクチャ

「**フィーチャーベースのサービスレイヤー・アーキテクチャ**」

#### Clean Architectureとの相違点

- **依存性の逆転(DI)は使用しない**
- **UI → Service → Repository の直線的依存関係**
- **シンプルで理解しやすい構造を優先**

#### レイヤー構造

```text
UI Layer (Flutter Widgets/Pages)
    ↓
Business Services Layer  
    ↓
Repository Layer (Data Access)
```

## 品質基準

### analysis_options.yamlで定義された品質基準

#### 厳格な型チェック

```yaml
analyzer:
  strong-mode:
    implicit-casts: false
    implicit-dynamic: false
  language:
    strict-casts: true
    strict-inference: true
    strict-raw-types: true
```

#### 重要なLintルール

- `always_declare_return_types`: 戻り値型の明示必須
- `always_specify_types`: 型の明示必須
- `prefer_double_quotes`: ダブルクォート使用
- `prefer_final_locals`: ローカル変数のfinal推奨
- `public_member_api_docs`: パブリックAPIのドキュメント必須
- `require_trailing_commas`: トレイリングコンマ必須
- `prefer_expression_function_bodies`: 式関数の推奨

### 必須品質チェック

1. **コミット前チェック**: `dart analyze && dart format && flutter test`
2. **型安全性**: 暗黙的キャスト禁止、厳格な型推論
3. **コード生成**: `dart run build_runner watch` での継続監視
4. **テストカバレッジ**: `flutter test --coverage` での品質確認

## 実装パターン

### モデルクラス実装規則

#### 基本テンプレート

```dart
import "package:json_annotation/json_annotation.dart";
import "../../../core/base/base.dart";

part "[model_name].g.dart";

/// [日本語での説明]
@JsonSerializable()
class [ModelName] extends BaseModel {
  /// コンストラクタ
  [ModelName]({
    // required parameters first
    required this.property1,
    required this.property2,
    // optional parameters
    this.optionalProperty,
    this.createdAt,
    this.updatedAt,
    super.id,
    super.userId,
  });

  /// JSONからインスタンスを作成
  factory [ModelName].fromJson(Map<String, dynamic> json) => _$[ModelName]FromJson(json);

  /// [プロパティの日本語説明]
  PropertyType property1;

  /// [プロパティの日本語説明]
  PropertyType property2;

  /// [オプションプロパティの説明]
  OptionalType? optionalProperty;

  /// 作成日時
  DateTime? createdAt;

  /// 更新日時
  DateTime? updatedAt;

  @override
  String get tableName => "[table_name]";

  /// JSONに変換
  @override
  Map<String, dynamic> toJson() => _$[ModelName]ToJson(this);
}
```

### Enum実装規則

#### 基本テンプレート

```dart
/// [Enumの日本語説明]
@JsonEnum()
enum [EnumName] {
  /// [値の説明]
  value1("value1"),
  
  /// [値の説明]
  value2("value2");

  const [EnumName](this.value);

  /// [Enum名]の値
  final String value;

  @override
  String toString() => value;

  /// 日本語での表示名を取得
  String get displayName {
    switch (this) {
      case [EnumName].value1:
        return "値1の日本語名";
      case [EnumName].value2:
        return "値2の日本語名";
    }
  }
}
```

### サービスクラス実装規則

#### 依存性注入パターン

```dart
@loggerComponent
class [ServiceName] with LoggerMixin {
  /// コンストラクタ
  [ServiceName]({
    [RepositoryType]? repository1,
    [RepositoryType]? repository2,
  }) : _repository1 = repository1 ?? [RepositoryType](),
       _repository2 = repository2 ?? [RepositoryType]();

  final [RepositoryType] _repository1;
  final [RepositoryType] _repository2;

  @override
  String get loggerComponent => "[ServiceName]";
}
```

#### ログ記録パターン

```dart
Future<Result> methodName(Parameters params) async {
  logInfoMessage(ServiceInfo.operationStarted, params);
  
  try {
    // 実装
    logInfoMessage(ServiceInfo.operationSuccessful, result);
    return result;
  } catch (e, stackTrace) {
    logErrorMessage(ServiceError.operationFailed, params, e, stackTrace);
    rethrow;
  }
}
```

## 命名規則

### ファイル・ディレクトリ命名

- **ファイル名**: `snake_case.dart`
- **ディレクトリ名**: `snake_case`
- **生成ファイル**: `original_name.g.dart`

### Dart言語要素命名

- **クラス名**: `PascalCase`
- **変数・メソッド名**: `camelCase`
- **定数**: `camelCase` (lowerCamelCase)
- **Enum値**: `camelCase`
- **プライベートメンバ**: `_camelCase`

### データベース・JSON関連

- **テーブル名**: `snake_case` (複数形)
- **カラム名**: `snake_case`
- **JSONキー**: `snake_case`

### 特定パターン命名

- **Repository**: `[EntityName]Repository`
- **Service**: `[FeatureName]Service`
- **Model**: `[EntityName]` (単数形)
- **DTO**: `[Purpose]Dto` または `[EntityName]Info`

## エラーハンドリング

### 標準パターン

```dart
try {
  logInfoMessage(ServiceInfo.operationStarted);
  // メイン処理
  logInfoMessage(ServiceInfo.operationSuccessful);
  return result;
} catch (e, stackTrace) {
  logErrorMessage(ServiceError.operationFailed, null, e, stackTrace);
  rethrow; // 上位レイヤーに委ねる
}
```

## 文書化ガイドライン

### コメント作成規則

```dart
/// [機能の概要説明]
///
/// [詳細な説明]
/// [使用例や注意点]
///
/// [param1] パラメータ1の説明
/// [param2] パラメータ2の説明
/// 
/// Returns: 戻り値の説明
///
/// Throws: 発生する可能性のある例外
ReturnType methodName(Param1Type param1, Param2Type param2) {
  // 実装
}
```

### TODOコメント規則

```dart
// TODO(dev): 一般的な開発タスク
// TODO(ui): UI関連の改善
// TODO(performance): パフォーマンス改善
// TODO(security): セキュリティ関連
```

## 推奨コーディングパターン

### インポート順序

```dart
// 1. Dart標準ライブラリ
import "dart:async";

// 2. Flutter関連
import "package:flutter/material.dart";

// 3. 外部パッケージ
import "package:riverpod/riverpod.dart";

// 4. プロジェクト内相対インポート
import "../../../core/base/base.dart";
import "../models/model.dart";
```

### クラス内順序

```dart
class ExampleClass {
  // 1. コンストラクタ
  // 2. ファクトリコンストラクタ
  // 3. フィールド（private -> public）
  // 4. ゲッター・セッター
  // 5. メソッド（private -> public）
  // 6. オーバーライドメソッド
}
```

### ログレベル使用

- **logDebug**: 開発時のみの詳細情報
- **logInfo**: 正常な処理フロー
- **logWarning**: 問題の可能性があるが継続可能
- **logError**: エラー・例外（リリース時も記録）

## 禁止事項

### 絶対に避けるべきコード

1. **暗黙的キャスト・型推論**: `analysis_options.yaml`で禁止
2. **未処理の例外**: 必ず適切にハンドリング
3. **プライベートメンバの直接アクセス**: 適切なアクセサ使用
4. **マジックナンバー**: 定数として定義
5. **英語コメント**: すべて日本語で記述

### 推奨されないパターン

1. **過度に複雑な継承階層**: シンプルな構造を維持
2. **グローバル状態**: Riverpodによる状態管理を使用
3. **同期的I/O処理**: 非同期処理を原則とする
