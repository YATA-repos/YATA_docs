# YATA Frontend Prototype - 技術アーキテクチャ詳細

## 1. アーキテクチャ概要

### 1.1 アーキテクチャパターン

- **パターン**: Client-Side Rendering (CSR) with Static Site Generation (SSG)
- **ルーティング**: Next.js App Router
- **状態管理**: React Hook + Local State（将来的にZustand/Redux Toolkit統合可能）
- **データフロー**: 単方向データフロー（Reactパターン）

### 1.2 レイヤー構造

```
Presentation Layer (UI Components)
    ↓
Business Logic Layer (Custom Hooks)
    ↓
Data Layer (Mock Data / Future API Integration)
```

---

## 2. 技術スタック詳細

### 2.1 コアフレームワーク

#### Next.js 15.2.4

- **App Router**: 最新のNext.jsルーティングシステム
- **Turbopack**: 高速バンドラー（開発時）
- **自動最適化**: 画像・フォント・コード分割
- **SSR/SSG**: サーバーサイドレンダリング対応

#### React 19.0.0

- **React Server Components**: 将来的な統合準備
- **Concurrent Features**: 非同期レンダリング
- **Hooks**: useState, useEffect, usePathname等活用
- **TypeScript**: 完全型安全

### 2.2 スタイリングシステム

#### Tailwind CSS 4.1.11

- **Utility-First**: 効率的なスタイリング
- **カスタムテーマ**: プロジェクト専用カラーパレット
- **レスポンシブ**: モバイルファースト設計
- **Dark Mode**: クラスベース対応準備済み

```css
/* カスタムカラーパレット */
primary: #2563eb     /* blue-600 */
secondary: #1d4ed8   /* blue-700 */
accent: #3b82f6      /* blue-500 */
success: #16a34a     /* green-600 */
warning: #ca8a04     /* yellow-600 */
danger: #dc2626      /* red-600 */
```

### 2.3 UIコンポーネントライブラリ

#### shadcn/ui + Radix UI

- **基盤**: Radix UI プリミティブ
- **カスタマイズ**: Tailwind CSSによる完全制御
- **アクセシビリティ**: WCAG 2.1 AA準拠
- **コンポーネント**: Button, Card, Table, Select等

#### Lucide React

- **アイコンライブラリ**: 1000+ SVGアイコン
- **一貫性**: 統一されたデザイン言語
- **軽量**: Tree-shakingによる最適化

### 2.4 データ可視化

#### Recharts 3.0.2

- **チャートタイプ**: Bar Chart, Pie Chart, Line Chart
- **レスポンシブ**: 自動サイズ調整
- **カスタマイズ**: 完全な見た目制御
- **アニメーション**: スムーズなトランジション

---

## 3. ファイル・ディレクトリ構造

### 3.1 App Routerディレクトリ

```
app/
├── favicon.ico          # ファビコン
├── globals.css          # グローバルスタイル（Tailwind含む）
├── layout.tsx           # ルートレイアウト
├── page.tsx             # ホームページ（/）
├── loading.tsx          # ローディングUI
├── inventory-mode.tsx   # 在庫概要モード
├── order-mode.tsx       # 注文作成モード
├── inventory/           # 在庫管理ページ群
│   ├── page.tsx        # メインページ (/inventory)
│   └── loading.tsx     # ローディングUI
├── orders/              # 注文履歴ページ群
│   ├── page.tsx        # メインページ (/orders)
│   └── loading.tsx     # ローディングUI
├── order-status/        # 注文状況ページ群
│   └── page.tsx        # メインページ (/order-status)
└── sales/               # 売上分析ページ群
    └── page.tsx        # メインページ (/sales)
```

### 3.2 Componentsディレクトリ

```
components/
├── main-nav.tsx         # ナビゲーションコンポーネント
└── ui/                  # shadcn/ui コンポーネント群
    ├── badge.tsx        # バッジ
    ├── button.tsx       # ボタン
    ├── card.tsx         # カード
    ├── chart.tsx        # チャート
    ├── input.tsx        # 入力フィールド
    ├── progress.tsx     # プログレスバー
    ├── select.tsx       # セレクトボックス
    ├── table.tsx        # テーブル
    └── tabs.tsx         # タブ
```

### 3.3 設定ファイル

```
Root/
├── next.config.ts       # Next.js設定
├── tailwind.config.ts   # Tailwind CSS設定
├── components.json      # shadcn/ui設定
├── tsconfig.json        # TypeScript設定
├── eslint.config.mjs    # ESLint設定
├── postcss.config.mjs   # PostCSS設定
└── package.json         # 依存関係・スクリプト
```

---

## 4. データ構造・型定義

### 4.1 在庫アイテム型

```typescript
interface InventoryItem {
  id: string
  name: string              // 商品名
  category: string          // カテゴリー
  currentStock: number      // 現在在庫数
  minStock: number          // 最小在庫数
  unit: string              // 単位（kg, 枚, 個等）
  unitPrice: number         // 単価
  status: 'available' | 'low' | 'critical'  // 在庫ステータス
  estimatedDays?: number    // 推定使用可能日数
}
```

### 4.2 注文アイテム型

```typescript
interface OrderItem {
  id: string
  name: string              // 商品名
  price: number             // 単価
  quantity: number          // 数量
  subtotal: number          // 小計
}

interface Order {
  id: string                // 注文番号
  items: OrderItem[]        // 注文アイテム一覧
  subtotal: number          // 小計
  tax: number               // 消費税
  total: number             // 合計
  status: 'preparing' | 'completed' | 'cancelled'
  timestamp: Date           // 注文日時
}
```

### 4.3 売上データ型

```typescript
interface SalesData {
  date: string              // 日付
  revenue: number           // 売上
}

interface ProductSales {
  name: string              // 商品名
  value: number             // 販売数量
  revenue: number           // 売上
}
```

---

## 5. レスポンシブデザイン

### 5.1 ブレークポイント

```css
/* Tailwind CSS ブレークポイント */
sm: 640px      /* タブレット（縦） */
md: 768px      /* タブレット（横） */
lg: 1024px     /* デスクトップ（小） */
xl: 1280px     /* デスクトップ（大） */
2xl: 1536px    /* ワイドスクリーン */
```

### 5.2 レスポンシブ戦略

#### モバイル (< 768px)

- **ナビゲーション**: ボトムナビゲーション
- **レイアウト**: 単一カラム
- **フォントサイズ**: 小（readable）
- **タッチターゲット**: 44px以上

#### タブレット (768px - 1024px)

- **ナビゲーション**: トップ + ボトム
- **レイアウト**: 2カラムグリッド
- **フォントサイズ**: 中
- **密度**: 中程度

#### デスクトップ (> 1024px)

- **ナビゲーション**: トップナビゲーション
- **レイアウト**: 3-4カラムグリッド
- **フォントサイズ**: 大
- **密度**: 高

---

## 6. パフォーマンス最適化

### 6.1 Next.js最適化機能

- **自動コード分割**: ページレベル + コンポーネントレベル
- **画像最適化**: next/image による自動最適化
- **フォント最適化**: next/font による自動最適化
- **プリフェッチ**: Link コンポーネントによる自動プリフェッチ

### 6.2 Bundle最適化

- **Tree Shaking**: 未使用コード削除
- **Minification**: コード最小化
- **Gzip圧縮**: 自動圧縮
- **CDN**: Vercel Edge Network活用

### 6.3 レンダリング最適化

- **React.memo**: 不要な再レンダリング防止
- **useMemo/useCallback**: 重い計算のメモ化
- **Lazy Loading**: 遅延ローディング
- **Suspense**: 非同期コンポーネント

---

## 7. 開発者体験

### 7.1 開発ツール

- **TypeScript**: 型安全性・IntelliSense
- **ESLint**: コード品質・一貫性
- **Prettier**: コードフォーマッター（推奨）
- **Hot Reload**: 即座のフィードバック

### 7.2 ビルドツール

- **Turbopack**: 高速開発サーバー
- **SWC**: 高速TypeScript/JSXコンパイラー
- **PostCSS**: CSS処理パイプライン
- **Vercel**: シームレスデプロイ

---

## 8. 将来的な拡張ポイント

### 8.1 状態管理強化

```typescript
// Zustand導入例
interface AppState {
  inventory: InventoryItem[]
  currentOrder: Order | null
  user: User | null
  // ...
}
```

### 8.2 API統合

```typescript
// API層抽象化
interface ApiClient {
  getInventory(): Promise<InventoryItem[]>
  createOrder(order: Order): Promise<Order>
  getSalesData(range: DateRange): Promise<SalesData[]>
}
```

### 8.3 認証システム

```typescript
// NextAuth.js統合
import NextAuth from "next-auth"
import { authOptions } from "@/lib/auth"

export default NextAuth(authOptions)
```

---

このアーキテクチャは、スケーラビリティ・保守性・パフォーマンスを重視して設計されており、小規模なプロトタイプから本格的なプロダクションアプリケーションへの発展を見据えています。
