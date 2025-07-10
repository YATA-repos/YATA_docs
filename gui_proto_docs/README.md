# YATA Frontend Prototype ドキュメントセット

## 📋 概要

このディレクトリには、YATA Frontend Prototype (`yata_front_proto`) プロジェクトの包括的な分析・説明ドキュメントが含まれています。小規模フードスタンド・レストラン向けの在庫・注文管理システムのWebプロトタイプに関する技術的詳細、実装内容、使用方法を網羅しています。

---

## 📚 ドキュメント一覧

### 1. [PROJECT_OVERVIEW.md](./PROJECT_OVERVIEW.md)

**プロジェクト概要・基本仕様**

- **内容**: プロジェクトの目的、技術スタック、主要機能の概要
- **対象**: プロジェクト理解を深めたい全ての開発者
- **ハイライト**:
  - Next.js 15.2.4 + React 19.0.0 + TypeScript
  - 小規模飲食店向けの実用的機能セット
  - 在庫管理・注文処理・売上分析の統合システム

### 2. [TECHNICAL_ARCHITECTURE.md](./TECHNICAL_ARCHITECTURE.md)

**技術アーキテクチャ詳細**

- **内容**: アーキテクチャパターン、技術スタック詳細、ファイル構造
- **対象**: 技術的深堀りや拡張を検討する開発者
- **ハイライト**:
  - Client-Side Rendering + SSG
  - shadcn/ui + Tailwind CSS デザインシステム
  - レスポンシブ・アクセシビリティ対応
  - パフォーマンス最適化戦略

### 3. [COMPONENTS_PAGES_REFERENCE.md](./COMPONENTS_PAGES_REFERENCE.md)

**コンポーネント・ページリファレンス**

- **内容**: 各ページ・コンポーネントの詳細実装、機能、特徴
- **対象**: 実装詳細を理解したい開発者、コードレビューワー
- **ハイライト**:
  - 注文作成モード・在庫管理・売上分析ページの詳細
  - ナビゲーション・UIコンポーネントシステム
  - データフロー・状態管理パターン
  - パフォーマンス最適化実装

### 4. [UI_SYSTEM_DATA_GUIDE.md](./UI_SYSTEM_DATA_GUIDE.md)

**UIシステム・データ構造ガイド**

- **内容**: デザインシステム、コンポーネントバリエーション、データ構造
- **対象**: UI開発者、デザイナー、データ設計者
- **ハイライト**:
  - カラーパレット・タイポグラフィ・レイアウトシステム
  - サンプルデータ構造（在庫・注文・売上）
  - アニメーション・レスポンシブパターン
  - アクセシビリティ対応ガイド

---

## 🎯 プロジェクト要約

### 基本情報

- **プロジェクト名**: YATA Frontend Prototype
- **技術スタック**: Next.js 15.2.4, React 19.0.0, TypeScript, Tailwind CSS
- **目的**: 小規模飲食店向けの統合管理システムのWebプロトタイプ
- **特徴**: レスポンシブデザイン、日本語UI、実用的な機能セット

### 主要機能

1. **注文管理システム**
   - リアルタイム注文作成
   - 注文履歴・検索機能
   - 注文状況確認

2. **在庫管理システム**
   - 在庫状況一覧・警告システム
   - 詳細在庫管理・統計ダッシュボード
   - カテゴリ別管理・発注機能

3. **売上分析システム**
   - 期間別売上分析
   - グラフ・チャートによる可視化
   - 商品別・時間帯別分析

### 技術的特徴

- **モダンスタック**: 最新のReact・Next.js生態系
- **型安全性**: TypeScriptによる堅牢な実装
- **デザインシステム**: shadcn/ui + Tailwind CSSによる統一感
- **レスポンシブ**: デスクトップ・モバイル完全対応
- **パフォーマンス**: Turbopack・React 19最適化

---

## 🚀 開発環境セットアップ

### 前提条件

- Node.js 18.0.0 以上
- npm または yarn または pnpm

### セットアップ手順

```bash
# プロジェクトディレクトリに移動
cd yata_front_proto

# 依存関係のインストール
npm install

# 開発サーバーの起動
npm run dev

# ブラウザで確認
# http://localhost:3000
```

### 使用可能なコマンド

```bash
# 開発サーバー起動（Turbopack使用）
npm run dev

# プロダクションビルド
npm run build

# プロダクションサーバー起動
npm start

# ESLintによるコードチェック
npm run lint
```

---

## 📱 主要画面・機能

### ナビゲーション

- **デスクトップ**: 上部水平ナビゲーション
- **モバイル**: 下部ボトムナビゲーション
- **レスポンシブ**: 自動切り替え

### 主要ページ

1. **ホーム** (`/`): エントリーポイント
2. **注文履歴** (`/orders`): 過去の注文管理
3. **在庫管理** (`/inventory`): 詳細在庫管理
4. **売上分析** (`/sales`): 売上データ可視化

### 特別モード

- **注文作成モード**: メニュー選択・注文カート
- **在庫概要モード**: 在庫状況・警告表示
- **注文状況** (`/order-status`): 現在の注文確認

---

## 🎨 UIデザインシステム

### カラーテーマ

- **プライマリ**: 青系統（#2563eb, #1d4ed8, #3b82f6）
- **セマンティック**: 成功（緑）、警告（黄）、危険（赤）
- **ベース**: グレースケール・白背景

### コンポーネント

- **shadcn/ui**: Radix UIベースの高品質コンポーネント
- **Lucide React**: 1000+ 統一アイコンセット
- **Recharts**: データ可視化チャート

### レスポンシブ

- **モバイルファースト**: 640px → 768px → 1024px → 1280px
- **フレキシブルグリッド**: 1-4カラム自動調整
- **タッチフレンドリー**: モバイル最適化

---

## 📊 データ構造

### 在庫アイテム

```typescript
interface InventoryItem {
  id: string
  name: string              // 商品名
  category: string          // カテゴリー
  currentStock: number      // 現在在庫
  minStock: number          // 最小在庫
  unit: string              // 単位
  unitPrice: number         // 単価
  status: 'available' | 'low' | 'critical'
}
```

### 注文データ

```typescript
interface Order {
  id: string                // 注文ID
  orderNumber: string       // 注文番号 (#1046)
  items: OrderItem[]        // 注文アイテム
  subtotal: number          // 小計
  tax: number               // 消費税
  total: number             // 合計
  status: 'preparing' | 'completed' | 'cancelled'
  createdAt: Date
}
```

### 売上データ

```typescript
interface SalesData {
  date: string              // 日付
  revenue: number           // 売上
  orderCount: number        // 注文数
  avgOrderValue: number     // 平均客単価
}
```

---

## 🔧 カスタマイズ・拡張

### 追加機能の実装

#### 新しいページの追加

1. `app/` ディレクトリに新しいディレクトリを作成
2. `page.tsx` ファイルを追加
3. `components/main-nav.tsx` にナビゲーション項目を追加

#### 新しいコンポーネントの追加

1. `components/ui/` に新しいコンポーネントを作成
2. shadcn/ui パターンに従って実装
3. TypeScript型定義を追加

#### データ構造の拡張

1. 型定義を更新
2. サンプルデータを更新
3. 関連コンポーネントを修正

### バックエンド統合

- **API統合**: REST API・GraphQL対応準備済み
- **認証**: NextAuth.js等との統合可能
- **データベース**: PostgreSQL・MongoDB等と連携可能

---

## 📈 パフォーマンス

### 最適化機能

- **自動コード分割**: ページレベル + コンポーネントレベル
- **画像最適化**: next/image による自動最適化
- **フォント最適化**: next/font による自動最適化
- **Tree Shaking**: 未使用コード削除

### ビルドサイズ

- **JavaScript**: 最適化済み（圧縮後）
- **CSS**: Tailwind CSS + PurgeCSS
- **アセット**: 自動最適化

---

## 🔍 今後の発展可能性

### 技術的拡張

- **PWA**: プログレッシブWebアプリ化
- **オフライン**: Service Worker統合
- **リアルタイム**: WebSocket・SSE統合
- **モバイルアプリ**: React Native移植

### 機能拡張

- **顧客管理**: 会員制度・ポイントシステム
- **スタッフ管理**: 勤怠・権限管理
- **キッチン連携**: 調理指示・ステータス管理
- **決済統合**: クレジットカード・電子マネー

### ビジネス拡張

- **マルチテナント**: 複数店舗対応
- **フランチャイズ**: チェーン店管理
- **分析強化**: AI・機械学習統合
- **サードパーティ**: POS・会計システム連携

---

## 📞 サポート・参考資料

### 公式ドキュメント

- [Next.js Documentation](https://nextjs.org/docs)
- [React Documentation](https://react.dev/)
- [Tailwind CSS](https://tailwindcss.com/docs)
- [shadcn/ui](https://ui.shadcn.com/)

### 開発ツール

- [TypeScript](https://www.typescriptlang.org/docs/)
- [Recharts](https://recharts.org/en-US/)
- [Lucide React](https://lucide.dev/guide/packages/lucide-react)

---

**📝 ドキュメント作成日**: 2025年7月2日  
**🔄 最終更新**: プロジェクト分析完了時  
**👤 作成者**: Claude Code AI Assistant

このドキュメントセットは、YATA Frontend Prototypeプロジェクトの包括的な理解と効率的な開発を支援するために作成されました。
