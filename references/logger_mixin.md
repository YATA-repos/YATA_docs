# LoggerMixin詳細ドキュメント

## 概要

LoggerMixinは、YATAプロジェクトにおけるログ記録の可読性と保守性を大幅に向上させるために開発されたMixinパターン実装です。既存のLogServiceをラップし、より簡潔で直感的なログ記録APIを提供します。

## 開発背景

### 課題

従来のLogService使用では以下の問題がありました：

```dart
// 従来の冗長なログ記録
LogService.debug("BaseRepository", "Finding single entity in table: users");
LogService.error("BaseRepository", "Failed to create entity", null, error, stackTrace);
```

**問題点**：

- コンポーネント名の重複記述
- 長い関数呼び出し（約50文字以上）
- 可読性の低下
- タイプミスによるコンポーネント名の不整合

### 解決策

LoggerMixinによる簡潔で一貫性のあるログ記録：

```dart
// LoggerMixin使用後の簡潔なログ記録
logDebug("Finding single entity in table: users");
logError("Failed to create entity", null, error, stackTrace);
```

**改善点**：

- 約50%の文字数削減
- コンポーネント名の自動設定
- 統一されたインターフェース
- 設定忘れの防止

## アーキテクチャ設計

### 設計原則

1. **既存LogServiceとの完全互換性**: 後方互換性を維持
2. **段階的導入**: クラス単位での適用が可能
3. **自動化**: コンポーネント名の自動設定
4. **型安全性**: コンパイル時チェック

### クラス構造

```dart
// LoggerComponentアノテーション
class LoggerComponent {
  const LoggerComponent([this.name]);
  final String? name;
}

// LoggerMixin本体
mixin LoggerMixin {
  String get loggerComponent; // 抽象getter（実装必須）
  
  // 簡潔なログメソッド群
  void logDebug(String message, [String? messageJa]);
  void logInfo(String message, [String? messageJa]);
  void logWarning(String message, [String? messageJa]);
  void logError(String message, [String? messageJa, Object? error, StackTrace? stackTrace]);
  
  // 事前定義メッセージ用メソッド
  void logInfoMessage(LogMessage logMessage, [Map<String, String>? params]);
  void logWarningMessage(LogMessage logMessage, [Map<String, String>? params]);
  void logErrorMessage(LogMessage logMessage, [Map<String, String>? params, Object? error, StackTrace? stackTrace]);
}
```

## 実装パターン

### パターン1: BaseRepositoryでの自動適用

BaseRepositoryレベルでLoggerMixinを適用することで、14個のRepositoryクラスで自動的に利用可能：

```dart
@loggerComponent
abstract class BaseRepository<T, ID> with LoggerMixin {
  @override
  String get loggerComponent => runtimeType.toString().split("<")[0];
  
  // 全CRUDメソッドで簡潔なログ記録が可能
  Future<T?> create(T entity) async {
    logDebug("Creating entity in table: $tableName");
    try {
      // 処理...
      logInfo("Entity created successfully");
      return result;
    } catch (error, stackTrace) {
      logError("Failed to create entity", null, error, stackTrace);
      rethrow;
    }
  }
}
```

**利点**：

- 全Repositoryクラスで一貫したログ記録
- 各クラス名が自動的にコンポーネント名として設定
- 追加実装不要

### パターン2: Serviceクラスでの手動適用

各Serviceクラスで個別にLoggerMixinを適用：

```dart
@loggerComponent
class InventoryService with LoggerMixin {
  @override
  String get loggerComponent => "InventoryService";
  
  Future<void> updateStock(String materialId, Decimal quantity) async {
    logInfo("Starting stock update for material: $materialId");
    
    try {
      // ビジネスロジック
      logInfo("Stock updated successfully");
    } catch (error, stackTrace) {
      logError("Stock update failed", null, error, stackTrace);
      rethrow;
    }
  }
}
```

**利点**：

- クラス固有のコンポーネント名設定
- ビジネスロジックに特化したログ記録

## 実装済みクラス

### Repositoryレイヤー（自動適用）

BaseRepositoryを継承する全14クラスで自動的にLoggerMixin機能が利用可能：

#### Analytics

- `DailySummaryRepository`

#### Inventory

- `MaterialCategoryRepository`
- `MaterialRepository`
- `RecipeRepository`

#### Menu

- `MenuCategoryRepository`
- `MenuItemRepository`

#### Order

- `OrderRepository`
- `OrderItemRepository`

#### Stock

- `PurchaseRepository`
- `PurchaseItemRepository`
- `StockAdjustmentRepository`
- `StockTransactionRepository`

各Repositoryクラスで以下のようなログ出力が可能：

```dart
// StockTransactionRepositoryの場合
logDebug("Finding transactions for material: $materialId");
// 出力: [StockTransactionRepository] DEBUG: Finding transactions for material: mat123
```

### Serviceレイヤー（手動適用済み）

以下6つのServiceクラスにLoggerMixinが適用済み：

1. **AnalyticsService**: 分析・統計処理のログ記録
2. **InventoryService**: 在庫管理操作のログ記録
3. **MenuService**: メニュー管理操作のログ記録
4. **OrderService**: 注文処理のログ記録
5. **CartService**: カート操作のログ記録
6. **KitchenService**: 調理管理のログ記録

## 使用方法

### 基本的な使用

```dart
@loggerComponent
class ExampleService with LoggerMixin {
  @override
  String get loggerComponent => "ExampleService";
  
  Future<void> performOperation() async {
    // デバッグログ（開発時のみ）
    logDebug("Starting operation");
    
    // 情報ログ
    logInfo("Operation in progress");
    
    // 多言語メッセージ
    logInfo("Process completed", "処理が完了しました");
    
    try {
      // リスクのある処理
      await riskyOperation();
    } catch (error, stackTrace) {
      // エラーログ（リリース時も保存）
      logError("Operation failed", "操作に失敗しました", error, stackTrace);
      rethrow;
    }
  }
}
```

### 事前定義メッセージの使用

```dart
class UserService with LoggerMixin {
  @override
  String get loggerComponent => "UserService";
  
  Future<void> authenticateUser(String email) async {
    try {
      // 認証処理
      logInfoMessage(
        AuthInfo.loginSuccessful,
        {"email": email}
      );
    } catch (error, stackTrace) {
      logErrorMessage(
        AuthError.loginFailed,
        {"email": email, "error": error.toString()},
        error,
        stackTrace
      );
    }
  }
}
```

## パフォーマンス特性

### メモリ使用量

- **追加オーバーヘッド**: 実質的になし
- **メソッド呼び出し**: LogServiceの静的メソッドへの薄いラッパー
- **コンポーネント名取得**: runtimeType使用時に軽微な処理コスト

### 実行時性能

LoggerMixin使用前後の性能比較：

```dart
// 従来方式
LogService.debug("Component", "Message"); // 直接呼び出し

// LoggerMixin使用
logDebug("Message"); // 1回の追加メソッド呼び出し
// → LogService.debug(loggerComponent, "Message");
```

**性能影響**: 1回の追加メソッド呼び出しのみ（マイクロ秒オーダー）

### runtimeType使用の考慮事項

BaseRepositoryでのruntimeType使用：

```dart
String get loggerComponent => runtimeType.toString().split("<")[0];
```

- **処理コスト**: 文字列操作が各ログ呼び出し時に実行
- **頻度**: 通常のアプリケーションでは問題なし
- **代替案**: 必要に応じて静的文字列への変更が可能

## 設定忘れ防止メカニズム

### コンパイル時チェック

LoggerMixinの抽象getterにより、実装忘れを防止：

```dart
mixin LoggerMixin {
  String get loggerComponent; // 実装必須
}

class ExampleService with LoggerMixin {
  // loggerComponentの実装を忘れるとコンパイルエラー
  // Error: Missing concrete implementation of 'getter mixin LoggerMixin.loggerComponent'
}
```

### @loggerComponentアノテーション

可読性向上と意図の明示：

```dart
@loggerComponent  // このクラスがLoggerMixinを使用することを明示
class ExampleService with LoggerMixin {
  @override
  String get loggerComponent => "ExampleService";
}
```

## 移行ガイド

### 既存コードからの移行

#### Step 1: LoggerMixinの適用

```dart
// 移行前
class UserService {
  Future<void> createUser() async {
    LogService.info("UserService", "Creating user");
  }
}

// 移行後
@loggerComponent
class UserService with LoggerMixin {
  @override
  String get loggerComponent => "UserService";
  
  Future<void> createUser() async {
    logInfo("Creating user");
  }
}
```

#### Step 2: ログ呼び出しの置換

```dart
// 置換パターン
LogService.debug("Component", "message")     → logDebug("message")
LogService.info("Component", "message")      → logInfo("message")
LogService.warning("Component", "message")   → logWarning("message")
LogService.error("Component", "message", null, e) → logError("message", null, e)
```

### 段階的移行戦略

1. **新規クラス**: LoggerMixinを最初から適用
2. **既存クラス**: 機能追加・修正時に段階的に移行
3. **重要クラス**: Repository/Serviceから優先的に移行

## トラブルシューティング

### よくある問題

#### 1. loggerComponentの実装忘れ

**症状**：

```
Error: Missing concrete implementation of 'getter mixin LoggerMixin.loggerComponent'
```

**解決方法**：

```dart
class ExampleService with LoggerMixin {
  @override
  String get loggerComponent => "ExampleService"; // この行を追加
}
```

#### 2. アノテーションエラー

**症状**：

```
Error: Annotation must be either a const variable reference or const constructor invocation
```

**解決方法**：

```dart
// ❌ 間違い
@loggerComponent("ExampleService")

// ✅ 正しい
@loggerComponent
```

#### 3. 期待したコンポーネント名が出力されない

**症状**: ログに想定外のコンポーネント名が表示される

**原因**: runtimeType使用時の型パラメータ

**解決方法**：

```dart
// BaseRepositoryの場合は自動的に適切な名前を設定
// 例: "StockRepository<Stock, String>" → "StockRepository"

// Serviceクラスの場合は明示的に設定
@override
String get loggerComponent => "InventoryService"; // 明示的な名前
```

## ベストプラクティス

### 1. 適切なコンポーネント名

```dart
// ✅ 良い例：明確で一意な名前
@override
String get loggerComponent => "UserAuthService";

// ❌ 悪い例：曖昧な名前
@override
String get loggerComponent => "Service";
```

### 2. ログメッセージの設計

```dart
// ✅ 良い例：具体的で行動指向
logInfo("User authentication completed for: $email");
logError("Failed to save user profile", null, error, stackTrace);

// ❌ 悪い例：曖昧で情報不足
logInfo("Done");
logError("Error");
```

### 3. エラーハンドリング

```dart
// ✅ 良い例：包括的なエラー情報
try {
  await performOperation();
  logInfo("Operation completed successfully");
} catch (error, stackTrace) {
  logError(
    "Operation failed: ${error.runtimeType}",
    "操作に失敗しました",
    error,
    stackTrace
  );
  rethrow;
}
```

### 4. 多言語対応

```dart
// ✅ 良い例：英語・日本語併用
logInfo("User registered successfully", "ユーザー登録が完了しました");
logWarning("Invalid email format", "メールアドレスの形式が正しくありません");

// ❌ 悪い例：日本語のみ
logInfo("ユーザー登録完了"); // 英語メッセージがない
```

## 今後の拡張予定

### 1. 自動コンポーネント名生成

```dart
// 将来的な改善：アノテーションからの自動生成
@GenerateLogger("UserService")
class UserService with LoggerMixin {
  // loggerComponentの自動実装
}
```

### 2. ログレベル別メソッド拡張

```dart
// 将来的な機能：条件付きログ
logDebugIf(condition, "Message");
logInfoOnce("One-time message");
```

### 3. 構造化ログ対応

```dart
// 将来的な機能：構造化データ
logStructured("user.login", {
  "userId": "123",
  "timestamp": DateTime.now(),
  "success": true
});
```

## まとめ

LoggerMixinは、YATAプロジェクトにおけるログ記録の品質と開発効率を大幅に向上させる重要な機能です。

### 主要な利点

1. **可読性向上**: 約50%の文字数削減
2. **一貫性確保**: 統一されたログ記録インターフェース
3. **保守性向上**: コンポーネント名の自動管理
4. **品質向上**: コンパイル時チェックによる設定忘れ防止

### 適用範囲

- ✅ **完了**: BaseRepository（14クラス）
- ✅ **完了**: Serviceレイヤー（6クラス）
- 🔄 **今後**: 必要に応じて他のクラスへの展開

LoggerMixinの継続的な活用により、YATAアプリケーションの開発効率と運用品質の向上を実現していきます。
