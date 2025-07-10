# YATA Frontend Prototype - UIシステム・データ構造ガイド

## 1. デザインシステム詳細

### 1.1 カラーパレット

#### メインカラー

```css
/* Primary Colors (青系) */
--primary: #2563eb          /* blue-600 - メインアクション */
--primary-foreground: #ffffff

--secondary: #1d4ed8        /* blue-700 - セカンダリアクション */
--secondary-foreground: #ffffff

--accent: #3b82f6           /* blue-500 - アクセント */
--accent-foreground: #ffffff
```

#### セマンティックカラー

```css
/* Status Colors */
--success: #16a34a          /* green-600 - 成功・在庫あり */
--success-foreground: #f0fdf4

--warning: #ca8a04          /* yellow-600 - 警告・在庫少 */
--warning-foreground: #fefce8

--danger: #dc2626           /* red-600 - 危険・緊急 */
--danger-foreground: #fef2f2
```

#### ベースカラー

```css
/* Base Colors */
--background: #ffffff
--foreground: #0f172a

--card: #ffffff
--card-foreground: #0f172a

--popover: #ffffff
--popover-foreground: #0f172a

--muted: #f1f5f9
--muted-foreground: #64748b

--border: #e2e8f0
--input: #e2e8f0
--ring: #2563eb
```

### 1.2 タイポグラフィ

#### フォントファミリー

```css
/* Geist Sans - UI Text */
--font-geist-sans: 'Geist', system-ui, -apple-system, sans-serif

/* Geist Mono - Code/Numbers */
--font-geist-mono: 'GeistMono', 'Courier New', monospace
```

#### フォントサイズ階層

```css
/* Text Sizes */
text-xs: 0.75rem     /* 12px - 補助情報 */
text-sm: 0.875rem    /* 14px - 説明文 */
text-base: 1rem      /* 16px - 本文 */
text-lg: 1.125rem    /* 18px - 小見出し */
text-xl: 1.25rem     /* 20px - 見出し */
text-2xl: 1.5rem     /* 24px - 大見出し */
text-3xl: 1.875rem   /* 30px - タイトル */
```

### 1.3 レイアウトシステム

#### グリッドシステム

```css
/* Responsive Grid */
grid-cols-1          /* モバイル: 1カラム */
md:grid-cols-2       /* タブレット: 2カラム */
lg:grid-cols-3       /* デスクトップ: 3カラム */
xl:grid-cols-4       /* ワイド: 4カラム */
```

#### スペーシング

```css
/* Spacing Scale (4px unit) */
gap-1: 0.25rem       /* 4px */
gap-2: 0.5rem        /* 8px */
gap-4: 1rem          /* 16px */
gap-6: 1.5rem        /* 24px */
gap-8: 2rem          /* 32px */

/* Padding/Margin */
p-2: 0.5rem          /* 8px */
p-4: 1rem            /* 16px */
p-6: 1.5rem          /* 24px */
```

#### 境界線・影

```css
/* Border Radius */
rounded-sm: 0.125rem     /* 2px */
rounded: 0.25rem         /* 4px */
rounded-md: 0.375rem     /* 6px */
rounded-lg: 0.5rem       /* 8px */
rounded-full: 9999px     /* 完全な円 */

/* Shadows */
shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05)
shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1)
shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1)
```

---

## 2. コンポーネントバリエーション

### 2.1 Button Variants

#### デフォルト（Primary）

```typescript
<Button>メインアクション</Button>
// bg-primary text-white hover:bg-primary/90
```

#### アウトライン

```typescript
<Button variant="outline">セカンダリアクション</Button>
// border border-input bg-background hover:bg-accent
```

#### Ghost

```typescript
<Button variant="ghost">テキストボタン</Button>
// hover:bg-accent hover:text-accent-foreground
```

#### サイズバリエーション

```typescript
<Button size="sm">小ボタン</Button>      // h-9 px-3
<Button size="default">標準ボタン</Button> // h-10 px-4
<Button size="lg">大ボタン</Button>      // h-11 px-8
<Button size="icon">🔍</Button>         // h-10 w-10
```

### 2.2 Badge Variants

#### ステータスバッジ

```typescript
// 成功（在庫あり）
<Badge className="bg-success/10 text-success hover:bg-success/20">
  在庫あり
</Badge>

// 警告（在庫少）
<Badge className="bg-warning/10 text-warning hover:bg-warning/20">
  在庫少
</Badge>

// 危険（緊急）
<Badge className="bg-danger/10 text-danger hover:bg-danger/20">
  <AlertTriangle className="h-3 w-3 mr-1" />
  緊急
</Badge>
```

### 2.3 Card Layouts

#### 基本カード

```typescript
<Card>
  <CardHeader>
    <CardTitle>タイトル</CardTitle>
    <CardDescription>説明文</CardDescription>
  </CardHeader>
  <CardContent>
    {/* コンテンツ */}
  </CardContent>
  <CardFooter>
    {/* フッター */}
  </CardFooter>
</Card>
```

#### 統計カード

```typescript
<Card>
  <CardHeader className="pb-2">
    <CardTitle className="text-sm font-medium">総在庫アイテム</CardTitle>
  </CardHeader>
  <CardContent>
    <div className="text-2xl font-bold">42</div>
    <p className="text-xs text-muted-foreground">前月比 +3</p>
  </CardContent>
</Card>
```

---

## 3. サンプルデータ構造

### 3.1 在庫データ

#### InventoryItem

```typescript
interface InventoryItem {
  id: string
  name: string
  category: '肉類' | 'パン類' | '飲料' | '野菜' | '果物' | '調理済食品'
  currentStock: number
  minStock: number
  unit: 'kg' | '枚' | '個' | 'L' | 'g'
  unitPrice: number
  status: 'available' | 'low' | 'critical'
  estimatedDays?: number
  lastUpdated: Date
}

// サンプルデータ
const sampleInventory: InventoryItem[] = [
  {
    id: "inv-001",
    name: "鶏肉（生）",
    category: "肉類",
    currentStock: 4.5,
    minStock: 2.0,
    unit: "kg",
    unitPrice: 1200,
    status: "available",
    lastUpdated: new Date("2023-05-08T10:30:00")
  },
  {
    id: "inv-002",
    name: "レモン",
    category: "果物",
    currentStock: 0.5,
    minStock: 2.0,
    unit: "kg",
    unitPrice: 350,
    status: "critical",
    estimatedDays: 1,
    lastUpdated: new Date("2023-05-08T09:15:00")
  }
]
```

### 3.2 注文データ

#### OrderItem & Order

```typescript
interface MenuItem {
  id: string
  name: string
  price: number
  category: 'メイン料理' | 'サイドメニュー' | 'ドリンク' | 'デザート'
  available: boolean
}

interface OrderItem {
  menuItemId: string
  name: string
  price: number
  quantity: number
  subtotal: number
}

interface Order {
  id: string
  orderNumber: string  // #1046 形式
  items: OrderItem[]
  subtotal: number
  tax: number          // 消費税10%
  total: number
  status: 'preparing' | 'completed' | 'cancelled'
  createdAt: Date
  completedAt?: Date
}

// サンプルデータ
const sampleMenu: MenuItem[] = [
  {
    id: "menu-001",
    name: "チキンラップ",
    price: 850,
    category: "メイン料理",
    available: true
  },
  {
    id: "menu-002",
    name: "ファラフェルボウル",
    price: 975,
    category: "メイン料理",
    available: true
  },
  {
    id: "menu-003",
    name: "アイスコーヒー",
    price: 425,
    category: "ドリンク",
    available: true
  }
]

const sampleOrder: Order = {
  id: "order-001",
  orderNumber: "#1046",
  items: [
    {
      menuItemId: "menu-001",
      name: "チキンラップ",
      price: 850,
      quantity: 1,
      subtotal: 850
    },
    {
      menuItemId: "menu-003",
      name: "アイスコーヒー",
      price: 425,
      quantity: 2,
      subtotal: 850
    }
  ],
  subtotal: 1700,
  tax: 170,
  total: 1870,
  status: "preparing",
  createdAt: new Date("2023-05-08T10:52:00")
}
```

### 3.3 売上分析データ

#### SalesData & Analytics

```typescript
interface DailySales {
  date: string         // "5/1" 形式
  revenue: number
  orderCount: number
  avgOrderValue: number
}

interface ProductSales {
  name: string
  units: number
  revenue: number
  percentage: number
}

interface HourlySales {
  timeSlot: string     // "10-11時" 形式
  revenue: number
  orderCount: number
}

// サンプルデータ
const dailySalesData: DailySales[] = [
  { date: "5/1", revenue: 32500, orderCount: 18, avgOrderValue: 1806 },
  { date: "5/2", revenue: 28900, orderCount: 16, avgOrderValue: 1806 },
  { date: "5/3", revenue: 41200, orderCount: 23, avgOrderValue: 1791 },
  { date: "5/4", revenue: 35600, orderCount: 20, avgOrderValue: 1780 },
  { date: "5/5", revenue: 52300, orderCount: 29, avgOrderValue: 1803 },
  { date: "5/6", revenue: 48100, orderCount: 27, avgOrderValue: 1781 },
  { date: "5/7", revenue: 43700, orderCount: 25, avgOrderValue: 1748 },
  { date: "5/8", revenue: 48750, orderCount: 29, avgOrderValue: 1681 }
]

const productSalesData: ProductSales[] = [
  { name: "チキンラップ", units: 32, revenue: 27200, percentage: 32 },
  { name: "ファラフェルボウル", units: 18, revenue: 17550, percentage: 18 },
  { name: "ベジバーガー", units: 15, revenue: 15750, percentage: 15 },
  { name: "アイスコーヒー", units: 25, revenue: 10625, percentage: 25 },
  { name: "レモネード", units: 10, revenue: 3750, percentage: 10 }
]
```

---

## 4. アニメーション・トランジション

### 4.1 Tailwind CSS Animations

#### ホバーエフェクト

```css
/* Button Hover */
.hover\:bg-primary\/90:hover {
  background-color: rgb(37 99 235 / 0.9);
  transition: background-color 0.2s ease-in-out;
}

/* Card Hover */
.hover\:bg-muted\/50:hover {
  background-color: rgb(241 245 249 / 0.5);
  transition: background-color 0.2s ease-in-out;
}
```

#### フォーカス状態

```css
/* Focus Ring */
.focus-visible\:ring-2:focus-visible {
  outline: 2px solid transparent;
  outline-offset: 2px;
  box-shadow: 0 0 0 2px rgb(37 99 235);
}
```

### 4.2 カスタムアニメーション

#### アコーディオン

```css
@keyframes accordion-down {
  from { height: 0 }
  to { height: var(--radix-accordion-content-height) }
}

@keyframes accordion-up {
  from { height: var(--radix-accordion-content-height) }
  to { height: 0 }
}

.animate-accordion-down {
  animation: accordion-down 0.2s ease-out;
}

.animate-accordion-up {
  animation: accordion-up 0.2s ease-out;
}
```

---

## 5. レスポンシブデザインパターン

### 5.1 グリッドレイアウト

#### メニューグリッド

```typescript
// モバイル: 1カラム, タブレット: 2カラム, デスクトップ: 3カラム
<div className="grid gap-4 sm:grid-cols-2 lg:grid-cols-3">
  {menuItems.map(item => (
    <MenuCard key={item.id} item={item} />
  ))}
</div>
```

#### 統計カード

```typescript
// モバイル: 1カラム, デスクトップ: 4カラム
<div className="grid gap-4 md:grid-cols-4">
  <StatsCard title="総在庫アイテム" value="42" change="+3" />
  <StatsCard title="在庫警告" value="3" change="緊急 1件" />
  <StatsCard title="発注中" value="5" change="到着予定 2件" />
  <StatsCard title="在庫金額" value="¥124,850" change="-2.5%" />
</div>
```

### 5.2 ナビゲーションパターン

#### デスクトップナビゲーション

```typescript
<nav className="hidden md:flex items-center gap-5 text-sm font-medium ml-6">
  {/* 水平ナビゲーション */}
</nav>
```

#### モバイルナビゲーション

```typescript
<div className="fixed bottom-0 left-0 z-10 w-full border-t bg-background md:hidden">
  {/* ボトムナビゲーション */}
</div>
```

---

## 6. アクセシビリティ対応

### 6.1 キーボードナビゲーション

#### フォーカス管理

```typescript
// タブインデックス設定
<Button
  tabIndex={0}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      handleClick()
    }
  }}
>
  アクション
</Button>
```

#### スクリーンリーダー対応

```typescript
// aria-label設定
<Button
  aria-label="注文に商品を追加"
  onClick={() => addToOrder(item)}
>
  <PlusCircle className="h-5 w-5" />
</Button>

// role設定
<div role="status" aria-live="polite">
  {statusMessage}
</div>
```

### 6.2 コントラスト・視認性

#### カラーコントラスト

```css
/* 4.5:1 以上のコントラスト比確保 */
.text-primary {
  color: #2563eb;  /* 白背景で 4.5:1 */
}

.text-muted-foreground {
  color: #64748b;  /* 白背景で 4.5:1 */
}
```

#### アイコン + テキスト

```typescript
// 情報をアイコンだけでなくテキストでも提供
<Badge className="bg-danger/10 text-danger">
  <AlertTriangle className="h-3 w-3 mr-1" />
  緊急
</Badge>
```

---

## 7. パフォーマンス最適化

### 7.1 画像最適化

#### next/image使用

```typescript
import Image from "next/image"

<Image
  src="/logo.svg"
  alt="YATA Logo"
  width={180}
  height={38}
  priority
  className="dark:invert"
/>
```

### 7.2 コードスプリッティング

#### 動的インポート

```typescript
import dynamic from 'next/dynamic'

const ChartComponent = dynamic(() => import('./ChartComponent'), {
  loading: () => <div>チャート読み込み中...</div>,
  ssr: false
})
```

### 7.3 レンダリング最適化

#### メモ化

```typescript
const MemoizedTable = React.memo(({ data, onSort }: TableProps) => {
  return <Table>{/* テーブル内容 */}</Table>
})

const sortedData = useMemo(() => {
  return data.sort((a, b) => a[sortField] - b[sortField])
}, [data, sortField])
```

---

このガイドは、一貫性のあるUI開発とデータ構造の理解を支援し、プロジェクトの拡張性と保守性を確保するために設計されています。
