# LoggerMixin使用ガイドライン

## 概要

LoggerMixinを使用してアプリケーション全体で一貫したログ出力を行うためのガイドラインです。開発効率、運用監視、セキュリティを考慮した統一的なログ記録手法を定義します。

## 基本原則

### 1. 言語統一

- **全てのログメッセージは英語で記述**する
- 国際的なツール対応、検索効率、チーム拡張性を重視
- 日本語での説明が必要な場合はコード内コメントで対応

### 2. セキュリティ・プライバシー優先

- **個人情報を一切ログに含めない**
- メールアドレス、電話番号、住所等は部分マスクも禁止
- 代わりにハッシュ値やユーザーIDを使用

### 3. パフォーマンス配慮

- **ループ内でのログ出力は極力控える**
- 処理開始・終了時にまとめて出力
- 高頻度処理では間引きログまたは統計情報のみ出力

## ログレベル別使用ガイドライン

### Debug - 開発時詳細情報

**用途**: 開発・デバッグ時の詳細な処理状況
**出力環境**: 開発時のみ（リリース時は出力されない）
**頻度**: 制限なし

```dart
// ✅ 適切な使用例
logDebug('Processing validation rules for user registration');
logDebug('Cache lookup result: hit=${isHit}, key=$keyHash');
logDebug('API request payload size: ${payload.length} bytes');

// ❌ 避けるべき例
logDebug('Starting process'); // 情報不足
logDebug('User email: $email'); // 個人情報含む
```

**特別ルール**:

- **事前定義メッセージは使用禁止** - 全て独自メッセージで記述
- 開発者の理解促進を最優先とする

### Info - 重要な処理状況

**用途**: ビジネスロジックの主要な処理開始・完了
**出力環境**: 全環境
**頻度**: 中程度（数回～数十回/分）

```dart
// ✅ 適切な使用例
logInfo('Started user authentication process');
logInfo('Completed data synchronization: 1,250 records processed');
logInfo('User session established successfully');

// ❌ 避けるべき例
logInfo('Button clicked'); // trivialな処理
logInfo('Processing item 1 of 100'); // ループ内出力
```

### Warning - 問題の可能性

**用途**: 正常動作するが注意が必要な状況
**出力環境**: 全環境（リリース時はファイル保存）
**頻度**: 低頻度（数回/時間）

```dart
// ✅ 適切な使用例
logWarning('API response time exceeded threshold: 5.2 seconds');
logWarning('Cache miss detected, falling back to database query');
logWarning('Retry attempt 3 of 5 for failed network request');

// ❌ 避けるべき例
logWarning('Slow operation'); // 具体的な情報不足
logWarning('User entered invalid email: $email'); // 個人情報含む
```

### Error - 明確な問題発生

**用途**: 処理失敗、例外発生、システム障害
**出力環境**: 全環境（リリース時はファイル保存、即座にフラッシュ）
**頻度**: 非常に低頻度（数回/日以下が理想）

```dart
// ✅ 適切な使用例
logError('Database connection failed: timeout after 10 seconds', null, error, stackTrace);
logError('User authentication failed: invalid credentials provided');
logError('File upload rejected: size exceeds 10MB limit');

// ❌ 避けるべき例
logError('Something went wrong'); // 原因不明
logError('Login failed for user: $email'); // 個人情報含む
```

## メッセージフォーマットルール

### 独自メッセージのフォーマット

**基本構造**: `(動詞) (対象) (状態/結果) (具体的な情報)`

### 動詞の使い分け

#### 現在分詞 (-ing) - 進行中の処理

```dart
logInfo('Starting user registration process');
logInfo('Processing payment transaction');
logInfo('Validating input parameters');
logDebug('Loading configuration from environment variables');
```

#### 過去分詞 (-ed) - 完了した処理

```dart
logInfo('User registration completed successfully');
logInfo('Payment transaction processed');
logError('Input validation failed: required field missing');
logWarning('Configuration loaded with default values');
```

### 具体的な情報の含め方

```dart
// ✅ 良い例
logInfo('Batch processing completed: 1,500 records in 23.4 seconds');
logWarning('API rate limit approaching: 95 of 100 requests used');
logError('Database query timeout: exceeded 30 second limit');

// ❌ 悪い例
logInfo('Processing done');
logWarning('Rate limit issue');
logError('Query problem');
```

## 事前定義メッセージルール

### 事前定義が必要な条件

- **同一メッセージが2回以上出現**した場合
- **Info/Warning/Error レベル**のメッセージ（Debugは除外）
- **パラメータ化が必要**なメッセージ

### 事前定義メッセージの作成方法

```dart
class AuthMessages {
  static const LogMessage loginSuccess = LogMessage(
    'User authentication completed successfully: session_id={sessionId}'
  );
  
  static const LogMessage loginFailed = LogMessage(
    'User authentication failed: {reason}'
  );
  
  static const LogMessage sessionExpired = LogMessage(
    'User session expired: automatic logout initiated'
  );
}

// 使用例
logInfoMessage(AuthMessages.loginSuccess, {'sessionId': hashedSessionId});
logWarningMessage(AuthMessages.sessionExpired);
```

### 事前定義メッセージの命名規則

- **クラス名**: `{機能名}Messages` (例: `AuthMessages`, `PaymentMessages`)
- **定数名**: `{動作}{結果}` (例: `loginSuccess`, `paymentFailed`)

## セキュリティ・プライバシー配慮

### 個人情報の扱い

```dart
// ❌ 絶対禁止
logInfo('User login: email=$email, phone=$phone');
logError('Registration failed for user: $fullName');

// ✅ 安全な代替案
logInfo('User login: user_id=$userId');
logError('Registration failed: user_id=$userId, validation_error=$errorCode');

// ✅ 部分的な情報（ハッシュ化）
final emailHash = email.hashCode.abs().toString();
logDebug('Processing registration for user_hash=$emailHash');
```

### 機密情報の扱い

```dart
// ❌ 危険
logDebug('API key: $apiKey');
logInfo('Database password updated: $newPassword');

// ✅ 安全
logDebug('API key configured successfully');
logInfo('Database credentials updated successfully');
```

## パフォーマンス配慮

### ループ処理での使用方法

```dart
// ❌ 避けるべき
void processItems(List<Item> items) {
  for (final item in items) {
    logInfo('Processing item: ${item.id}'); // 毎回ログ出力
    // 処理
  }
}

// ✅ 推奨
void processItems(List<Item> items) {
  logInfo('Started batch processing: ${items.length} items');
  
  int processedCount = 0;
  int errorCount = 0;
  
  for (final item in items) {
    try {
      // 処理
      processedCount++;
    } catch (e) {
      errorCount++;
      if (errorCount <= 5) { // 最初の5件のみエラーログ
        logError('Item processing failed: item_position=$processedCount', null, e);
      }
    }
  }
  
  logInfo('Batch processing completed: $processedCount processed, $errorCount errors');
}
```

### 高頻度処理での間引きログ

```dart
// ✅ 統計的ログ出力
class StreamProcessor with LoggerMixin {
  int _processedCount = 0;
  DateTime _lastLogTime = DateTime.now();
  
  void processStreamData(Data data) {
    _processedCount++;
    
    // 10秒ごとまたは1000件ごとにログ出力
    final now = DateTime.now();
    if (now.difference(_lastLogTime).inSeconds >= 10 || _processedCount % 1000 == 0) {
      logInfo('Stream processing status: $_processedCount items processed');
      _lastLogTime = now;
    }
  }
}
```

## 実装例

### 基本的な使用パターン

```dart
@loggerComponent
class UserService with LoggerMixin {
  @override
  String get loggerComponent => 'UserService';
  
  Future<User> createUser(UserRegistrationData data) async {
    logInfo('Started user registration process');
    
    try {
      // バリデーション
      logDebug('Validating user registration data');
      await _validateUserData(data);
      
      // データベース保存
      logDebug('Saving user data to database');
      final user = await _saveUser(data);
      
      // 成功ログ
      logInfo('User registration completed successfully: user_id=${user.id}');
      return user;
      
    } on ValidationException catch (e) {
      logWarning('User registration validation failed: ${e.code}');
      rethrow;
    } catch (e, stackTrace) {
      logError('User registration failed: unexpected error', null, e, stackTrace);
      rethrow;
    }
  }
}
```

### 事前定義メッセージを活用したパターン

```dart
class PaymentMessages {
  static const LogMessage paymentStarted = LogMessage(
    'Payment processing started: amount={amount}, method={method}'
  );
  
  static const LogMessage paymentCompleted = LogMessage(
    'Payment processing completed: transaction_id={transactionId}'
  );
  
  static const LogMessage paymentFailed = LogMessage(
    'Payment processing failed: {reason}'
  );
}

@loggerComponent
class PaymentService with LoggerMixin {
  @override
  String get loggerComponent => 'PaymentService';
  
  Future<PaymentResult> processPayment(PaymentRequest request) async {
    logInfoMessage(PaymentMessages.paymentStarted, {
      'amount': request.amount.toString(),
      'method': request.method,
    });
    
    try {
      final result = await _executePayment(request);
      logInfoMessage(PaymentMessages.paymentCompleted, {
        'transactionId': result.transactionId,
      });
      return result;
    } catch (e, stackTrace) {
      logErrorMessage(PaymentMessages.paymentFailed, {
        'reason': e.toString(),
      }, e, stackTrace);
      rethrow;
    }
  }
}
```

## 開発フローでの使用方法

### Phase 1: 初期開発

1. **独自メッセージで迅速実装**

   ```dart
   logInfo('Started user authentication process');
   logError('Authentication failed: invalid credentials');
   ```

### Phase 2: コードレビュー時

1. **重複メッセージの特定**
2. **セキュリティチェック**（個人情報の確認）
3. **ログレベルの適切性確認**

### Phase 3: リファクタリング

1. **2回以上出現するメッセージの事前定義化**
2. **メッセージフォーマットの統一**

## チェックリスト

### 実装時チェック

- [ ] ログメッセージは英語で記述されているか
- [ ] 個人情報が含まれていないか
- [ ] 適切なログレベルが選択されているか
- [ ] ループ内でのログ出力を避けているか
- [ ] メッセージフォーマット「動詞 + 対象 + 状態 + 具体的情報」に従っているか

### コードレビュー時チェック

- [ ] 同一メッセージが2回以上出現していないか
- [ ] Debug以外で事前定義メッセージを使用すべき箇所はないか
- [ ] エラーハンドリングでスタックトレースが適切に記録されているか
- [ ] 機密情報がログに露出していないか

### リリース前チェック

- [ ] Warning/Errorレベルのログが適切な頻度か
- [ ] ログファイルサイズが想定範囲内か
- [ ] 本番環境でのログ分析要件を満たしているか
