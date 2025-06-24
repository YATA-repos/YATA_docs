# LogService詳細ドキュメント

## 概要

LogServiceは、YATAアプリケーション全体で使用される統一ログサービスです。エンタープライズレベルの機能を持つ本格的なログシステムとして設計されており、開発効率と運用品質の両方を向上させます。

### 設計思想

- **開発・デバッグ効率の向上**: 開発時はすべてのログを即座にコンソール出力
- **本番運用の信頼性**: リリース時は重要なログのみをファイル永続化
- **パフォーマンス最適化**: バッファリングと非同期処理による性能向上
- **運用性の確保**: ログローテーション、統計情報、クリーンアップ機能

## 主要機能

### 1. ログレベル管理

| レベル | 優先度 | 開発時 | リリース時 | 用途 |
|--------|--------|--------|------------|------|
| `debug` | 0 | ✅ コンソール | ❌ 出力なし | デバッグ情報 |
| `info` | 1 | ✅ コンソール | ❌ 出力なし | 一般情報 |
| `warning` | 2 | ✅ コンソール | ✅ ファイル | 注意事項 |
| `error` | 3 | ✅ コンソール | ✅ ファイル | エラー情報 |

### 2. プラットフォーム対応

サポートプラットフォーム：

- Android
- iOS  
- Windows
- macOS
- Linux

各プラットフォームに最適化されたログファイルパスを環境変数で設定可能。

### 3. 高度なパフォーマンス機能

#### バッファリング機能

- **バッファサイズ**: 100エントリ
- **フラッシュ間隔**: 5秒
- **即座フラッシュ**: エラーレベルログ発生時

#### ファイルローテーション

- **最大ファイルサイズ**: 10MB
- **ローテーション方式**: 日付ベース + 連番（例: `2024-06-21-mixed.1.log`）
- **自動削除**: 30日経過ファイルの自動クリーンアップ

## 基本的な使用方法

### 1. 初期化

```dart
// アプリケーション起動時に一度だけ実行
await LogService.initialize();

// 最小ログレベルを指定する場合
await LogService.initialize(minimumLevel: LogLevel.info);
```

### 2. LoggerMixin使用（推奨）

YATAプロジェクトでは、LoggerMixinを使用した簡潔なログ記録を推奨しています。

```dart
import "../../../core/utils/logger_mixin.dart";

// クラスでLoggerMixinを使用
@loggerComponent
class UserAuthService with LoggerMixin {
  @override
  String get loggerComponent => "UserAuthService";

  Future<void> login() async {
    logDebug("Login attempt started");
    
    try {
      // 認証処理
      logInfo("User authenticated successfully");
    } catch (error, stackTrace) {
      logError("Authentication failed", null, error, stackTrace);
    }
  }
}
```

### 3. 基本ログ出力（直接LogService使用）

```dart
// デバッグログ（開発時のみ出力）
LogService.debug("UserAuth", "Login attempt started");

// 情報ログ
LogService.info("UserAuth", "User authenticated successfully");

// 警告ログ（リリース時もファイル保存）
LogService.warning("UserAuth", "Invalid login attempt");

// エラーログ（リリース時もファイル保存、即座フラッシュ）
LogService.error("UserAuth", "Database connection failed", null, error, stackTrace);
```

### 4. 多言語対応ログ

```dart
// 英語・日本語併用メッセージ
LogService.info("Inventory", "Stock updated", "在庫が更新されました");
// 出力: "Stock updated (在庫が更新されました)"

// 英語のみの場合
LogService.info("Inventory", "Stock updated");
// 出力: "Stock updated"
```

### 5. 事前定義メッセージの使用

```dart
// LogMessage を使用したログ出力
LogService.errorWithMessage(
  "Repository", 
  RepositoryError.databaseConnectionFailed
);

// パラメータ付きメッセージ
LogService.errorWithMessage(
  "Repository",
  RepositoryError.recordNotFound,
  {"id": "12345", "table": "users"}
);
```

## LoggerMixin詳細

LoggerMixinは、YATAプロジェクトでの可読性向上とコード簡潔化のために開発された機能です。

### LoggerMixinの利点

| 従来のLogService | LoggerMixin |
|------------------|-------------|
| `LogService.debug("Component", "message")` | `logDebug("message")` |
| `LogService.error("Component", "Failed", null, e)` | `logError("Failed", null, e)` |
| コンポーネント名を毎回指定 | 自動的にコンポーネント名設定 |
| 文字数が長い | 約50%の文字数削減 |

### LoggerMixin実装パターン

#### 1. BaseRepositoryでの自動実装

BaseRepositoryを継承するすべてのRepositoryクラスで自動的にLoggerMixinが利用可能：

```dart
class StockRepository extends BaseRepository<Stock, String> {
  // loggerComponentは自動的に"StockRepository"に設定
  
  Future<Stock?> findById(String id) async {
    logDebug("Finding stock with ID: $id");  // 簡潔なログ記録
    
    try {
      // 処理...
      logInfo("Stock found successfully");
      return stock;
    } catch (error, stackTrace) {
      logError("Failed to find stock", null, error, stackTrace);
      rethrow;
    }
  }
}
```

#### 2. Serviceクラスでの手動実装

各Serviceクラスではmixinを手動で適用：

```dart
@loggerComponent
class InventoryService with LoggerMixin {
  @override
  String get loggerComponent => "InventoryService";
  
  Future<void> updateStock() async {
    logInfo("Starting stock update process");
    // 処理...
  }
}
```

### LoggerMixinメソッド一覧

#### 基本ログメソッド

```dart
// デバッグログ
logDebug("Debug message", "デバッグメッセージ");

// 情報ログ  
logInfo("Info message", "情報メッセージ");

// 警告ログ
logWarning("Warning message", "警告メッセージ");

// エラーログ
logError("Error message", "エラーメッセージ", error, stackTrace);
```

#### 事前定義メッセージメソッド

```dart
// 情報レベル
logInfoMessage(RepositoryInfo.entityCreated, {"table": "users"});

// 警告レベル
logWarningMessage(ValidationWarning.invalidData, {"field": "email"});

// エラーレベル
logErrorMessage(RepositoryError.databaseConnectionFailed, null, error, stackTrace);
```

### @loggerComponentアノテーション

クラスがLoggerMixinを使用することを明示するマーカー：

```dart
// アノテーション使用により可読性向上
@loggerComponent
class UserService with LoggerMixin {
  @override
  String get loggerComponent => "UserService";
}
```

### 実装済みクラス

LoggerMixinが既に適用されているクラス：

#### Repositoryレイヤー（14クラス）
- BaseRepository（自動適用）
- StockTransactionRepository, MaterialRepository, etc.

#### Serviceレイヤー（6クラス）
- AnalyticsService
- InventoryService
- MenuService
- OrderService
- CartService
- KitchenService

## 高度な機能

### 1. 動的ログレベル変更

```dart
// 実行時にログレベルを変更
LogService.setMinimumLevel(LogLevel.warning);
```

### 2. バッファ管理

```dart
// 手動でバッファをフラッシュ
await LogService.flushBuffer();

// アプリケーション終了時のクリーンアップ
await LogService.dispose();
```

### 3. ログ統計情報の取得

```dart
final stats = await LogService.getLogStats();
print("ログファイル数: ${stats['totalFiles']}");
print("総サイズ: ${stats['totalSizeMB']} MB");
print("バッファ長: ${stats['bufferLength']}");
print("最小レベル: ${stats['minimumLevel']}");
```

### 4. 古いログファイルのクリーンアップ

```dart
// 30日より古いログファイルを削除（デフォルト）
await LogService.cleanupOldLogs();

// 7日より古いログファイルを削除
await LogService.cleanupOldLogs(daysToKeep: 7);
```

## 設定・カスタマイズ

### 環境変数設定

`.env`ファイルで各プラットフォームのログパスを設定：

```env
# Android
LOG_PATH_ANDROID=/storage/emulated/0/Android/data/com.example.yata/files/logs

# iOS
LOG_PATH_IOS=/var/mobile/Containers/Data/Application/[APP_ID]/Documents/logs

# Windows
LOG_PATH_WINDOWS=C:\Users\[USERNAME]\AppData\Local\YATA\logs

# macOS
LOG_PATH_MACOS=/Users/[USERNAME]/Library/Application Support/YATA/logs

# Linux
LOG_PATH_LINUX=/home/[USERNAME]/.local/share/YATA/logs
```

### パフォーマンス調整可能パラメータ

LogService内で調整可能な定数：

```dart
// 最大ファイルサイズ（デフォルト: 10MB）
static const int _maxFileSize = 10 * 1024 * 1024;

// バッファサイズ（デフォルト: 100エントリ）
static const int _bufferSize = 100;

// フラッシュ間隔（デフォルト: 5秒）
static const int _flushInterval = 5;
```

## ログファイル形式

### ファイル命名規則

```
YYYY-MM-DD-mixed.log           # 基本ログファイル
YYYY-MM-DD-mixed.1.log         # ローテーション後のファイル
YYYY-MM-DD-mixed.2.log         # 2回目のローテーション
```

### ログエントリ形式

```
[2024-06-21T10:30:45.123456] [UserAuth] [INFO] User authenticated successfully
Error: DatabaseException: Connection timeout
StackTrace: #0 DatabaseService.connect (package:yata/core/infrastructure/database_service.dart:45:7)
---
```

## パフォーマンス特性

### メモリ使用量

- **バッファサイズ**: 最大100エントリ
- **メモリ使用量**: 典型的なログエントリで約10KB（100エントリ × 100B）
- **ガベージコレクション**: バッファクリア時に自動解放

### ディスク使用量

- **ログファイルサイズ**: 最大10MB/ファイル
- **ローテーション**: 自動的にファイル分割
- **自動削除**: 30日経過ファイルの削除

### 書き込み性能

- **バッファリング**: 100エントリ蓄積後の一括書き込み
- **非同期処理**: UIスレッドをブロックしない
- **再試行機能**: 書き込み失敗時に最大3回リトライ

## トラブルシューティング

### よくある問題と解決方法

#### 1. ログファイルが作成されない

**症状**: リリースビルドでログファイルが生成されない

**原因**:

- ログディレクトリの作成に失敗
- 権限不足

**解決方法**:

```dart
// 統計情報でエラーをチェック
final stats = await LogService.getLogStats();
if (stats.containsKey('error')) {
  print('ログサービスエラー: ${stats['error']}');
}
```

#### 2. パフォーマンスの問題

**症状**: ログ出力が頻繁でアプリが重い

**解決方法**:

```dart
// ログレベルを上げる
LogService.setMinimumLevel(LogLevel.warning);

// バッファを手動で制御
await LogService.flushBuffer();
```

#### 3. ディスク容量不足

**症状**: ログファイルが大量に蓄積される

**解決方法**:

```dart
// 古いログを定期的にクリーンアップ
await LogService.cleanupOldLogs(daysToKeep: 7);
```

## アーキテクチャ詳細

### クラス設計

```dart
class LogService {
  // シングルトンパターン
  static LogService? _instance;
  static LogService get instance;
  
  // 状態管理
  static bool _initialized;
  static String? _logDirectory;
  static LogLevel _minimumLevel;
  
  // バッファリング
  static final Queue<String> _logBuffer;
  static Timer? _flushTimer;
  static bool _isFlushInProgress;
}
```

### 処理フロー

1. **初期化フェーズ**

   ```
   initialize() → _setupLogDirectory() → _startFlushTimer()
   ```

2. **ログ出力フェーズ**

   ```
   log() → _log() → _formatMessage() → _addToBuffer()
   ```

3. **バッファフラッシュフェーズ**

   ```
   flushBuffer() → _writeBufferedEntry() → _writeWithRetry()
   ```

4. **ローテーションフェーズ**

   ```
   _writeBufferedEntry() → _rotateLogFile() → file.rename()
   ```

### スレッドセーフティ

- **バッファアクセス**: Queue操作は原子的
- **フラッシュ制御**: `_isFlushInProgress`フラグによる排他制御
- **ファイル操作**: 非同期処理による競合回避

## ベストプラクティス

### 1. LoggerMixinの使用（推奨）

```dart
// ✅ 良い例：LoggerMixinを使用
@loggerComponent
class OrderService with LoggerMixin {
  @override
  String get loggerComponent => "OrderService";
  
  Future<void> createOrder() async {
    logInfo("Creating new order");
    try {
      // 処理...
      logInfo("Order created successfully");
    } catch (error, stackTrace) {
      logError("Failed to create order", null, error, stackTrace);
    }
  }
}

// ❌ 悪い例：直接LogService使用（冗長）
class OrderService {
  Future<void> createOrder() async {
    LogService.info("OrderService", "Creating new order");
    try {
      // 処理...
      LogService.info("OrderService", "Order created successfully");
    } catch (error, stackTrace) {
      LogService.error("OrderService", "Failed to create order", null, error, stackTrace);
    }
  }
}
```

### 2. 適切なログレベルの選択

```dart
// ✅ 良い例
logDebug("Button clicked");                    // UI操作の詳細
logInfo("User login successful");              // 重要な状態変化
logWarning("Rate limit approaching");          // 潜在的問題
logError("Connection failed", null, error, stackTrace); // 深刻な問題

// ❌ 悪い例
logError("Button clicked");                    // 重要度が不適切
logDebug("Connection failed");                 // 重要な情報が見逃される
```

### 3. 適切なコンポーネント名

```dart
// ✅ 良い例：機能領域を明確に
LogService.info("UserAuth", "Login attempt");
LogService.info("InventoryService", "Stock updated");
LogService.info("OrderRepository", "Order created");

// ❌ 悪い例：曖昧な名前
LogService.info("Service", "Something happened");
LogService.info("Component", "Action performed");
```

### 4. エラー情報の適切な記録

```dart
// ✅ 良い例：包括的なエラー情報
try {
  await performDatabaseOperation();
} catch (error, stackTrace) {
  LogService.error(
    "DatabaseService",
    "Failed to perform operation",
    "データベース操作に失敗しました",
    error,
    stackTrace,
  );
}

// ❌ 悪い例：情報不足
catch (error) {
  LogService.error("DB", "Error");
}
```

### 5. パフォーマンス考慮

```dart
// ✅ 良い例：条件付きログ
if (kDebugMode) {
  LogService.debug("Performance", "Heavy operation took ${duration}ms");
}

// ✅ 良い例：事前定義メッセージの活用
LogService.errorWithMessage("Repository", RepositoryError.recordNotFound);

// ❌ 悪い例：毎回文字列構築
LogService.debug("Loop", "Processing item ${i} of ${total}"); // ループ内で頻繁実行
```

### 6. リソース管理

```dart
// アプリケーション開始時
await LogService.initialize();

// アプリケーション終了時
await LogService.dispose();

// 定期的なメンテナンス
Timer.periodic(Duration(days: 1), (_) async {
  await LogService.cleanupOldLogs();
});
```

## 関連コンポーネント

### LoggerMixin

`lib/core/utils/logger_mixin.dart`で定義：

```dart
mixin LoggerMixin {
  String get loggerComponent;
  
  void logDebug(String message, [String? messageJa]);
  void logInfo(String message, [String? messageJa]);
  void logWarning(String message, [String? messageJa]);
  void logError(String message, [String? messageJa, Object? error, StackTrace? stackTrace]);
  
  void logInfoMessage(LogMessage logMessage, [Map<String, String>? params]);
  void logWarningMessage(LogMessage logMessage, [Map<String, String>? params]);
  void logErrorMessage(LogMessage logMessage, [Map<String, String>? params, Object? error, StackTrace? stackTrace]);
}
```

### LoggerComponent Annotation

```dart
class LoggerComponent {
  const LoggerComponent([this.name]);
  final String? name;
}

const LoggerComponent loggerComponent = LoggerComponent();
```

### LogLevel Enum

`lib/core/constants/enums.dart`で定義：

```dart
enum LogLevel {
  debug(0, "debug", 500),
  info(1, "info", 800),
  warning(2, "warning", 900),
  error(3, "error", 1000);
  
  const LogLevel(this.priority, this.value, this.developerLevel);
  
  final int priority;
  final String value;
  final int developerLevel;
  
  bool get shouldPersistInRelease => priority >= 2;
}
```

### LogMessage Interface

`lib/core/error/base.dart`で定義：

```dart
abstract class LogMessage {
  String get messageEn;
  String get messageJa;
  String get combinedMessage;
  String withParams(Map<String, String> params);
}
```

## 今後の拡張予定

### 1. リモートログ送信機能

```dart
// 将来的な機能
LogService.enableRemoteLogging(
  endpoint: "https://api.example.com/logs",
  apiKey: "your-api-key",
);
```

### 2. ログ分析機能

```dart
// 将来的な機能
final analysis = await LogService.analyzeLogFiles();
print("エラー率: ${analysis.errorRate}%");
print("最頻エラー: ${analysis.topErrors}");
```

### 3. カスタムフォーマッタ

```dart
// 将来的な機能
LogService.setCustomFormatter((level, component, message) => {
  return "[${level.value}] $component: $message";
});
```

---

## まとめ

LogServiceは、開発効率と運用品質を両立させる本格的なエンタープライズログシステムです。適切に使用することで：

- **開発効率の向上**: 詳細なデバッグ情報による問題の迅速な特定
- **運用品質の確保**: 重要なログの永続化と自動管理
- **パフォーマンス最適化**: バッファリングによる性能影響の最小化
- **保守性の向上**: 統計情報とクリーンアップ機能による運用負荷軽減

継続的な改善により、YATAアプリケーションの品質向上に寄与していきます。
