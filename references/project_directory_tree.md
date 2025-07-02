# YATA プロジェクト ディレクトリ構造

YATA（Yet Another Task App）は、レストラン在庫管理システムのFlutterアプリケーションです。このドキュメントでは、プロジェクトのディレクトリ構造と各ディレクトリの役割について説明します。

## アーキテクチャ

### 概要

このプロジェクトは、一言で表すなら、「**フィーチャーベースの『サービスレイヤー・アーキテクチャ』(Feature-based Service Layer Architecture)**」を採用しています。ただし、このアーキテクチャと類似しているClean Architectureとの明確な違いは、「依存性の逆転は使わず、UI→Service→Repositoryという直線的な依存関係にしている」点です。

このアーキテクチャについては、以下のような言い換えも可能です：

- フィーチャーベース・レイヤードアーキテクチャ (Feature-based Layered Architecture)
- サービスレイヤー・アーキテクチャ (Service Layer Architecture)
- 直線的レイヤードアーキテクチャ (Linear Layered Architecture)

### レイヤー構造(依存関係)

```
UI Layer (Flutter Widgets/Pages)
    ↓
Business Services Layer  
    ↓
Repository Layer (Data Access)
```

## プロジェクトルート構造

```text
YATA/
├── CLAUDE.md                    # プロジェクト開発指示書
├── README.md                    # プロジェクト説明書
├── pubspec.yaml                 # Flutter依存関係設定
├── pubspec.lock                 # 依存関係バージョンロック
├── analysis_options.yaml        # 静的解析・linter設定
├── lib/                        # メインソースコード
├── android/                    # Android固有設定
├── ios/                        # iOS固有設定
├── web/                        # Web固有設定
├── windows/                    # Windows固有設定
├── linux/                      # Linux固有設定
├── macos/                      # macOS固有設定
├── docs/                       # プロジェクトドキュメント
├── _old_py_docs/               # 旧Python版ドキュメント（参考用）
└── _old_py_project/            # 旧Python版プロジェクト（移植元）
```

## lib/ ディレクトリ構造

### 全体構造

```text
lib/
├── main.dart                   # アプリケーションエントリーポイント
├── core/                       # コア機能・共通機能
├── features/                   # 機能別のビジネスロジック
├── routing/                    # アプリケーションルーティング
└── shared/                     # 共通UI要素・コンポーネント
```

### core/ ディレクトリ

```text
core/
├── core.dart                   # コアモジュールエクスポート
├── auth/                       # 認証機能
│   └── auth_service.dart
├── base/                       # 基底クラス
│   ├── base.dart               
│   ├── base_model.dart         # JSON機能付きベースモデル
│   ├── base_model.g.dart       # 自動生成コード
│   └── base_repository.dart    # CRUD+フィルタリング基底クラス
├── constants/                  # 定数・設定値
│   ├── constants.dart          
│   ├── config.dart             
│   ├── enums.dart              
│   ├── query_types.dart        
│   └── timezone.dart           
├── error/                      # エラー定義
│   ├── error.dart              
│   ├── base.dart               
│   ├── auth.dart               
│   ├── inventory.dart          
│   ├── order.dart              
│   └── repository.dart         
├── infrastructure/             # インフラ層
│   ├── offline/                # オフライン機能（未実装）
│   └── supabase/               # Supabase統合（未実装）
├── sync/                       # データ同期機能
│   └── models/
│       ├── sync_model.dart     
│       └── sync_model.g.dart   
└── utils/                      # ユーティリティ
    ├── log_service.dart        # ログサービス
    ├── logger_mixin.dart       # ログ機能ミックスイン
    └── query_utils.dart        # クエリユーティリティ        
```

### features/ ディレクトリ

各機能モジュールは以下の共通構造を持ちます：

```text
features/
├── analytics/                  # 分析機能
├── inventory/                  # 在庫管理機能
├── menu/                       # メニュー管理機能
├── order/                      # 注文管理機能
└── stock/                      # 在庫機能
    ├── dto/                    # DTOオブジェクト
    │   └── stock_dto.dart
    ├── models/                 # ドメインモデル
    │   ├── stock_model.dart
    │   └── stock_model.g.dart
    ├── presentation/           # UI層（未実装）
    ├── repositories/           # データアクセス層（実装済み）
    │   ├── purchase_repository.dart
    │   ├── purchase_item_repository.dart
    │   ├── stock_transaction_repository.dart
    │   └── stock_adjustment_repository.dart
    └── services/               # ビジネスロジック層（実装済み）
        └── analytics_service.dart

各機能内の共通構造：
[feature_name]/
├── dto/                        # Data Transfer Objects
│   └── [feature]_dto.dart
├── models/                     # ドメインモデル
│   ├── [feature]_model.dart
│   └── [feature]_model.g.dart  # 自動生成コード
├── presentation/               # UI層（未実装）
│   ├── providers/              # 状態管理プロバイダー
│   ├── screens/                # 画面・ページ
│   └── widgets/                # 再利用可能ウィジェット
├── repositories/               # データアクセス層（実装済み）
└── services/                   # ビジネスロジック層（実装状況は機能により異なる）
```

### その他のディレクトリ

```text
routing/                        # ルーティング（未実装）

shared/                         # 共通UI要素
├── layouts/                    # レイアウト（未実装）
├── themes/                     # テーマ（未実装）
└── widgets/                    # 共通ウィジェット（未実装）
```

## 主要ファイル

- **`lib/main.dart`**: アプリケーションエントリーポイント
- **`lib/core/base/base_model.dart`**: JSON機能付きベースモデルクラス
- **`lib/core/base/base_repository.dart`**: CRUD+フィルタリング機能付きベースリポジトリ
- **`CLAUDE.md`**: プロジェクト開発指示書
- **`pubspec.yaml`**: Flutter依存関係設定

## 実装状況

### 実装済み

- **基底システム**: `BaseModel`, `BaseRepository`, エラーハンドリング
- **モデルクラス**: 全機能（analytics、inventory、menu、order、stock）のドメインモデル
- **DTO**: 全機能のData Transfer Objects
- **リポジトリ層**: 12個のリポジトリクラスが実装済み
  - `analytics`: DailySummaryRepository
  - `inventory`: MaterialRepository, MaterialCategoryRepository, RecipeRepository
  - `menu`: MenuItemRepository, MenuCategoryRepository
  - `order`: OrderRepository, OrderItemRepository
  - `stock`: PurchaseRepository, PurchaseItemRepository, StockTransactionRepository, StockAdjustmentRepository
- **サービス層**: 6個のサービスクラスが実装済み
  - `analytics`: AnalyticsService
  - `inventory`: InventoryService
  - `menu`: MenuService
  - `order`: CartService, KitchenService, OrderService
  - `stock`: 未実装
- **コア機能**: 認証サービス、同期モデル、ユーティリティ（LoggerMixin含む）

### 未実装

- **UI層（presentation）**: プロバイダー、画面、ウィジェット（全機能）
- **サービス層**: stockサービス
- **インフラ層**: Supabase統合、オフライン機能
- **ルーティング**: アプリケーションナビゲーション
- **共通UI**: レイアウト、テーマ、共通ウィジェット
