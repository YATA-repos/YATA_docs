# YATA Frontend Prototype - コンポーネント・ページリファレンス

## 1. ページコンポーネント詳細

### 1.1 ホームページ (`app/page.tsx`)

#### 概要

- **役割**: アプリケーションのエントリーポイント
- **現状**: Next.jsデフォルトのランディングページ
- **将来**: ダッシュボード統合予定

#### 実装内容

```typescript
export default function Home() {
  return (
    <div className="grid grid-rows-[20px_1fr_20px]...">
      {/* Next.jsデフォルトコンテンツ */}
    </div>
  );
}
```

#### 改善ポイント

- ダッシュボード統合
- クイックアクセス機能
- 重要指標の表示

---

### 1.2 注文作成モード (`app/order-mode.tsx`)

#### 概要

- **役割**: リアルタイム注文作成インターフェース
- **レイアウト**: 2カラム（メニュー選択 + 注文カート）
- **主要機能**: メニュー選択、数量調整、税計算、注文確定

#### 実装内容

##### メニュー選択エリア

```typescript
// カテゴリフィルター
<div className="col-span-full flex overflow-auto pb-2 mb-2 gap-2">
  <Button variant="outline" className="rounded-full">すべて</Button>
  <Button variant="outline" className="rounded-full">メイン料理</Button>
  // ...
</div>

// メニューアイテム表示
<Card className="cursor-pointer hover:bg-muted/50 transition-colors">
  <CardContent className="p-3 flex justify-between items-center">
    <div>
      <h3 className="font-medium">チキンラップ</h3>
      <p className="text-sm text-muted-foreground">¥850</p>
    </div>
    <Button size="sm" variant="ghost">
      <PlusCircle className="h-5 w-5 text-primary" />
    </Button>
  </CardContent>
</Card>
```

##### 注文カートエリア

```typescript
// 数量調整コントロール
<div className="flex items-center gap-1">
  <Button size="icon" variant="outline" className="h-6 w-6 rounded-full">
    <Minus className="h-3 w-3" />
  </Button>
  <span className="w-6 text-center">1</span>
  <Button size="icon" variant="outline" className="h-6 w-6 rounded-full">
    <Plus className="h-3 w-3" />
  </Button>
</div>

// 価格計算表示
<div className="w-full flex justify-between items-center mb-4 pt-2 border-t font-medium text-lg">
  <span>合計</span>
  <span>¥2,365</span>
</div>
```

#### 特徴

- **リアルタイム計算**: 数量変更即座に反映
- **カテゴリフィルター**: メニュー絞り込み
- **レスポンシブグリッド**: デバイス対応
- **アクセシビリティ**: キーボード操作対応

---

### 1.3 詳細在庫管理ページ (`app/inventory/page.tsx`)

#### 概要

- **役割**: 完全な在庫管理システム
- **レイアウト**: 統計カード + タブ式テーブル
- **主要機能**: 在庫一覧、検索、フィルタ、警告システム

#### 実装内容

##### 統計ダッシュボード

```typescript
<div className="grid gap-4 md:grid-cols-4">
  <Card>
    <CardHeader className="pb-2">
      <CardTitle className="text-sm font-medium">総在庫アイテム</CardTitle>
    </CardHeader>
    <CardContent>
      <div className="text-2xl font-bold">42</div>
      <p className="text-xs text-muted-foreground">前月比 +3</p>
    </CardContent>
  </Card>
  // ... 他の統計カード
</div>
```

##### タブシステム

```typescript
<Tabs defaultValue="all" className="w-full">
  <TabsList className="p-1 bg-muted">
    <TabsTrigger value="all">すべて</TabsTrigger>
    <TabsTrigger value="ingredients">食材</TabsTrigger>
    <TabsTrigger value="supplies">備品</TabsTrigger>
    <TabsTrigger value="warnings">
      <div className="flex items-center">
        <span>警告</span>
        <span className="ml-2 bg-danger text-white text-xs rounded-full h-5 w-5 flex items-center justify-center">3</span>
      </div>
    </TabsTrigger>
  </TabsList>
  // ... TabsContent
</Tabs>
```

##### 在庫テーブル

```typescript
<Table>
  <TableHeader>
    <TableRow>
      <TableHead>商品名</TableHead>
      <TableHead>カテゴリー</TableHead>
      <TableHead>現在の在庫</TableHead>
      <TableHead>最小在庫</TableHead>
      <TableHead>単価</TableHead>
      <TableHead>状態</TableHead>
      <TableHead className="text-right">アクション</TableHead>
    </TableRow>
  </TableHeader>
  <TableBody>
    {/* 在庫アイテム一覧 */}
  </TableBody>
</Table>
```

#### 特徴

- **多次元フィルタリング**: カテゴリ、状態、検索
- **ソート機能**: 各列でのソート
- **バッチ操作**: 複数選択・一括操作
- **エクスポート**: CSV/Excel出力

---

### 1.4 注文履歴ページ (`app/orders/page.tsx`)

#### 概要

- **役割**: 過去の注文記録管理
- **レイアウト**: フィルターカード + 注文テーブル
- **主要機能**: 履歴検索、フィルタリング、詳細表示

#### 実装内容

##### フィルターシステム

```typescript
<div className="grid gap-4 md:grid-cols-4">
  <div className="space-y-2">
    <label className="text-sm font-medium">日付範囲</label>
    <Button variant="outline" className="w-full justify-start text-left font-normal">
      <Calendar className="mr-2 h-4 w-4" />
      2023/05/08
    </Button>
  </div>
  
  <div className="space-y-2">
    <label className="text-sm font-medium">ステータス</label>
    <Select defaultValue="all">
      <SelectTrigger>
        <SelectValue placeholder="すべて" />
      </SelectTrigger>
      <SelectContent>
        <SelectItem value="all">すべて</SelectItem>
        <SelectItem value="completed">完了</SelectItem>
        <SelectItem value="preparing">調理中</SelectItem>
        <SelectItem value="cancelled">キャンセル</SelectItem>
      </SelectContent>
    </Select>
  </div>
</div>
```

#### 特徴

- **高度な検索**: 複数条件組み合わせ
- **日付ピッカー**: カレンダー式選択
- **ステータス管理**: 注文進行状況
- **ページネーション**: 大量データ対応

---

### 1.5 売上分析ページ (`app/sales/page.tsx`)

#### 概要

- **役割**: 売上データ可視化・分析
- **レイアウト**: 期間選択 + 統計 + チャート群
- **主要機能**: 期間分析、トレンド表示、商品別分析

#### 実装内容

##### チャート実装

```typescript
import { BarChart, Bar, PieChart, Pie, ResponsiveContainer } from "recharts"

// 日別売上チャート
<ResponsiveContainer width="100%" height="100%">
  <BarChart data={dailySalesData}>
    <ChartXAxis dataKey="name" />
    <ChartYAxis />
    <ChartTooltip />
    <Bar dataKey="売上" fill="#0284c7" radius={[4, 4, 0, 0]} />
  </BarChart>
</ResponsiveContainer>

// 商品別売上円グラフ
<ResponsiveContainer width="100%" height="100%">
  <PieChart>
    <Pie
      data={productSalesData}
      cx="50%"
      cy="50%"
      labelLine={false}
      outerRadius={80}
      fill="#0284c7"
      dataKey="value"
      label={({ name, percent }) => `${name} ${(percent * 100).toFixed(0)}%`}
    >
      {productSalesData.map((entry, index) => (
        <Cell key={`cell-${index}`} fill={COLORS[index % COLORS.length]} />
      ))}
    </Pie>
  </PieChart>
</ResponsiveContainer>
```

#### 特徴

- **複数チャートタイプ**: 棒グラフ、円グラフ、線グラフ
- **インタラクティブ**: ホバー・クリック対応
- **期間カスタマイズ**: 柔軟な期間設定
- **エクスポート**: レポート出力

---

## 2. 共通コンポーネント詳細

### 2.1 ナビゲーション (`components/main-nav.tsx`)

#### MainNav（デスクトップ用）

```typescript
export function MainNav() {
  const pathname = usePathname()

  const navItems = [
    { name: "ホーム", href: "/", icon: Home, active: pathname === "/" },
    { name: "注文履歴", href: "/orders", icon: ListOrdered, active: pathname.startsWith("/orders") },
    { name: "売上分析", href: "/sales", icon: BarChart3, active: pathname.startsWith("/sales") },
  ]

  return (
    <nav className="hidden md:flex items-center gap-5 text-sm font-medium ml-6">
      {navItems.map((item) => (
        <Link key={item.href} href={item.href} className={cn(/* スタイリング */)}>
          <item.icon className="h-4 w-4" />
          {item.name}
        </Link>
      ))}
    </nav>
  )
}
```

#### MobileNav（モバイル用）

```typescript
export function MobileNav() {
  const pathname = usePathname()

  return (
    <div className="fixed bottom-0 left-0 z-10 w-full border-t bg-background md:hidden shadow-[0_-2px_10px_rgba(0,0,0,0.1)]">
      <div className="flex items-center justify-around h-16">
        {/* ナビゲーションアイテム */}
        <Link href="/order-status" className="flex flex-col items-center justify-center">
          <div className="bg-primary text-white p-3 rounded-full -mt-8 shadow-lg">
            <Clock className="h-6 w-6" />
          </div>
          <span className="text-xs mt-1">状況</span>
        </Link>
      </div>
    </div>
  )
}
```

#### 特徴

- **アクティブ状態**: 現在ページのハイライト表示
- **レスポンシブ**: デスクトップ・モバイル自動切り替え
- **アクセシビリティ**: キーボードナビゲーション
- **視覚的階層**: フローティングアクションボタン

---

## 3. UIコンポーネントシステム

### 3.1 shadcn/ui コンポーネント

#### Button (`components/ui/button.tsx`)

```typescript
const buttonVariants = cva(
  "inline-flex items-center justify-center rounded-md text-sm font-medium ring-offset-background transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground hover:bg-primary/90",
        destructive: "bg-destructive text-destructive-foreground hover:bg-destructive/90",
        outline: "border border-input bg-background hover:bg-accent hover:text-accent-foreground",
        secondary: "bg-secondary text-secondary-foreground hover:bg-secondary/80",
        ghost: "hover:bg-accent hover:text-accent-foreground",
        link: "text-primary underline-offset-4 hover:underline",
      },
      size: {
        default: "h-10 px-4 py-2",
        sm: "h-9 rounded-md px-3",
        lg: "h-11 rounded-md px-8",
        icon: "h-10 w-10",
      },
    },
    defaultVariants: {
      variant: "default",
      size: "default",
    },
  }
)
```

#### Card (`components/ui/card.tsx`)

```typescript
const Card = React.forwardRef<HTMLDivElement, React.HTMLAttributes<HTMLDivElement>>(
  ({ className, ...props }, ref) => (
    <div
      ref={ref}
      className={cn("rounded-lg border bg-card text-card-foreground shadow-sm", className)}
      {...props}
    />
  )
)

const CardHeader = React.forwardRef<HTMLDivElement, React.HTMLAttributes<HTMLDivElement>>(
  ({ className, ...props }, ref) => (
    <div ref={ref} className={cn("flex flex-col space-y-1.5 p-6", className)} {...props} />
  )
)
```

### 3.2 カスタムコンポーネント

#### ChartContainer (`components/ui/chart.tsx`)

```typescript
interface ChartContainerProps {
  children: React.ReactNode
  className?: string
}

export function ChartContainer({ children, className }: ChartContainerProps) {
  return (
    <div className={cn("w-full h-full", className)}>
      {children}
    </div>
  )
}
```

---

## 4. データフロー・状態管理

### 4.1 現在の実装

#### ローカル状態管理

```typescript
// 注文状態の例
const [orderItems, setOrderItems] = useState<OrderItem[]>([])
const [selectedCategory, setSelectedCategory] = useState<string>("all")

// 在庫フィルタの例  
const [searchTerm, setSearchTerm] = useState<string>("")
const [statusFilter, setStatusFilter] = useState<string>("all")
```

#### URLパラメータ活用

```typescript
// Next.js useSearchParams活用例
const searchParams = useSearchParams()
const pathname = usePathname()

// クエリパラメータでの状態管理
const currentMode = searchParams.get('mode') || 'order'
```

### 4.2 将来の拡張

#### グローバル状態管理（Zustand例）

```typescript
interface AppState {
  // 在庫状態
  inventory: InventoryItem[]
  inventoryLoading: boolean
  
  // 注文状態
  currentOrder: Order | null
  orderHistory: Order[]
  
  // UI状態
  sidebarOpen: boolean
  theme: 'light' | 'dark'
  
  // アクション
  addToOrder: (item: MenuItem) => void
  updateInventory: (id: string, updates: Partial<InventoryItem>) => void
  toggleSidebar: () => void
}
```

---

## 5. パフォーマンス考慮

### 5.1 レンダリング最適化

#### React.memo活用

```typescript
const MemoizedOrderItem = React.memo(({ item, onUpdate }: OrderItemProps) => {
  return (
    <div className="flex justify-between items-center">
      {/* アイテム表示 */}
    </div>
  )
})
```

#### useMemo/useCallback

```typescript
const filteredInventory = useMemo(() => {
  return inventory.filter(item => 
    item.name.toLowerCase().includes(searchTerm.toLowerCase()) &&
    (statusFilter === 'all' || item.status === statusFilter)
  )
}, [inventory, searchTerm, statusFilter])

const handleAddToOrder = useCallback((item: MenuItem) => {
  setOrderItems(prev => [...prev, { ...item, quantity: 1 }])
}, [])
```

### 5.2 バンドルサイズ最適化

#### 動的インポート

```typescript
const RechartsComponents = dynamic(() => import('recharts'), {
  loading: () => <div>チャート読み込み中...</div>,
  ssr: false
})
```

#### Tree Shaking対応

```typescript
// Good: 個別インポート
import { Button } from "@/components/ui/button"
import { Card, CardContent } from "@/components/ui/card"

// Avoid: 全体インポート
import * as UI from "@/components/ui"
```

---

このリファレンスは、各コンポーネントの役割・実装・最適化ポイントを網羅し、開発者が効率的にコードを理解・拡張できるよう設計されています。
