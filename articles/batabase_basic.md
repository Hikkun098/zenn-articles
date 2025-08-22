---
title: "データベース・SQL　基礎基本"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["SQL"]
published: true
---

pythonのSQLAlchemyの勉強と復習も兼ねてアウトプット！
覚えることがいっぱいあるので大変💦
今後も更新や修正をしていこうと思います。


## データベースとは何か？

### データベースの基本概念
データベースとは、**データを効率的に格納・管理・検索するためのシステム**です。

**身近な例**
- 図書館の蔵書管理システム
- 銀行の顧客管理システム
- ECサイトの商品・注文管理システム

### なぜデータベースが必要なのか？

**Excelとの比較：**
| 項目 | Excel | データベース |
|------|-------|-------------|
| データ量 | データ量に限界がある | 億単位のデータも扱える |
| 同時利用 | 複数人での編集は困難 | 多数のユーザーが同時アクセス可能 |
| データ整合性 | 手動で管理 | 自動で整合性を保証 |
| 処理速度 | 大量データでは遅い | 高速検索・処理 |
| セキュリティ | 基本的な保護のみ | 高度なアクセス制御 |

## データベースの種類

### リレーショナルデータベース（RDB）
最も一般的なデータベース形式。データを「表（テーブル）」の形で管理。
データベースと言われたらRDBのことを指しています。

**主要なRDBMS：**
- **MySQL**
- **PostgreSQL**
- **Oracle**
- **SQL Server**

### NoSQLデータベース
- **MongoDB** - ドキュメント型
- **Redis** - キー・バリュー型
- **Cassandra** - 列指向型

## SQLとは？

**SQL（Structured Query Language）** = データベースとやり取りするための言語

### SQLの4つの基本操作（CRUD）
- **C**reate（作成） - データの挿入
- **R**ead（読み取り） - データの検索・取得
- **U**pdate（更新） - データの変更
- **D**elete（削除） - データの削除

## データベースの基本構造

### テーブル設計の例：ECサイト

より規模が大きいシステムはとても複雑で
テーブル数ももっと多いと思われます。
経験がないため想像ですが、
Amazonの大きなECサイトを見ると商品テーブルしても
ジャンルごとなどに分割されていそうではあります。


#### 顧客テーブル（customers）
| customer_id | name | email | phone | created_at |
|-------------|------|-------|-------|------------|
| 1 | 田中太郎 | tanaka@email.com | 090-1234-5678 | 2024-01-15 |
| 2 | 佐藤花子 | sato@email.com | 080-9876-5432 | 2024-01-16 |

#### 商品テーブル（products）
| product_id | name | price | category | stock |
|------------|------|-------|----------|-------|
| 1 | iPhone15 | 120000 | スマートフォン | 50 |
| 2 | MacBook Pro | 250000 | ノートPC | 20 |

#### 注文テーブル（orders）
| order_id | customer_id | product_id | quantity | order_date |
|----------|-------------|------------|----------|------------|
| 1 | 1 | 1 | 2 | 2024-01-20 |
| 2 | 2 | 2 | 1 | 2024-01-21 |

### 重要な概念

#### 主キー（Primary Key）
- 各行を一意に識別するための列
- 重複不可、NULL不可
- 例：customer_id, product_id, order_id

#### 外部キー（Foreign Key）
- 他のテーブルの主キーを参照する列
- テーブル同士の関連を表現
- 例：orders テーブルの customer_id は customers テーブルを参照

## 基本的なSQL文

### データベース・テーブルの作成

```sql
-- データベースの作成
CREATE DATABASE shop_db;

-- データベースの使用
USE shop_db;

-- テーブルの作成
CREATE TABLE customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(20),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### データの挿入（INSERT）

```sql
-- 1件のデータを挿入
INSERT INTO customers (name, email, phone) 
VALUES ('田中太郎', 'tanaka@email.com', '090-1234-5678');

-- 複数件のデータを一度に挿入
INSERT INTO customers (name, email, phone) VALUES
    ('佐藤花子', 'sato@email.com', '080-9876-5432'),
    ('鈴木一郎', 'suzuki@email.com', '070-1111-2222'),
    ('高橋美咲', 'takahashi@email.com', '090-3333-4444');
```

### データの検索（SELECT）

```sql
-- 全データの取得
SELECT * FROM customers;

-- 特定の列のみ取得
SELECT name, email FROM customers;

-- 条件を指定して検索
SELECT * FROM customers WHERE name = '田中太郎';

-- 複数条件での検索
SELECT * FROM customers 
WHERE created_at >= '2024-01-01' AND phone LIKE '090%';

-- 並び替え
SELECT * FROM customers ORDER BY created_at DESC;

-- 件数制限
SELECT * FROM customers LIMIT 10;
```

### データの更新（UPDATE）

```sql
-- 特定の顧客の電話番号を更新
UPDATE customers 
SET phone = '090-9999-8888' 
WHERE customer_id = 1;

-- 複数の列を同時に更新
UPDATE customers 
SET name = '田中太郎（改名）', email = 'tanaka_new@email.com'
WHERE customer_id = 1;
```

### データの削除（DELETE）

```sql
-- 特定の顧客を削除
DELETE FROM customers WHERE customer_id = 1;

-- 条件に合致する複数データを削除
DELETE FROM customers WHERE created_at < '2024-01-01';

-- テーブルの全データを削除（注意！）
DELETE FROM customers;
```

### 集計関数

```sql
-- 顧客数をカウント
SELECT COUNT(*) FROM customers;

-- 商品の平均価格
SELECT AVG(price) FROM products;

-- 最高価格・最低価格
SELECT MAX(price), MIN(price) FROM products;

-- 価格の合計
SELECT SUM(price) FROM products;
```

### グループ化（GROUP BY）

```sql
-- カテゴリ別の商品数
SELECT category, COUNT(*) as product_count 
FROM products 
GROUP BY category;

-- カテゴリ別の平均価格
SELECT category, AVG(price) as avg_price 
FROM products 
GROUP BY category;

-- 月別の注文数
SELECT DATE_FORMAT(order_date, '%Y-%m') as month, COUNT(*) as order_count
FROM orders 
GROUP BY DATE_FORMAT(order_date, '%Y-%m');
```

### テーブル結合（JOIN）

```sql
-- 注文情報と顧客情報を結合
SELECT 
    o.order_id,
    c.name as customer_name,
    p.name as product_name,
    o.quantity,
    o.order_date
FROM orders o
INNER JOIN customers c ON o.customer_id = c.customer_id
INNER JOIN products p ON o.product_id = p.product_id;

-- 顧客別の注文金額合計
SELECT 
    c.name,
    SUM(p.price * o.quantity) as total_amount
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id
INNER JOIN products p ON o.product_id = p.product_id
GROUP BY c.customer_id, c.name;
```

### サブクエリ

```sql
-- 平均価格より高い商品を検索
SELECT * FROM products 
WHERE price > (SELECT AVG(price) FROM products);

-- 注文のない顧客を検索
SELECT * FROM customers 
WHERE customer_id NOT IN (SELECT DISTINCT customer_id FROM orders);
```

## インデックス

### インデックスとは？
データ検索を高速化するための仕組み。本の索引と同じ概念。

```sql
-- インデックスの作成
CREATE INDEX idx_customer_email ON customers(email);
CREATE INDEX idx_product_category ON products(category);

-- 複合インデックス
CREATE INDEX idx_order_customer_date ON orders(customer_id, order_date);

-- インデックスの削除
DROP INDEX idx_customer_email ON customers;
```

### ACID特性

この説明を何回も読んでいるけど、
いまいちピンとこない…

- **A**tomicity（原子性） - 全て成功するか全て失敗するか
- **C**onsistency（一貫性） - データの整合性を保つ
- **I**solation（独立性） - 他の処理の影響を受けない
- **D**urability（永続性） - コミット後は確実に保存


### 正規化
データの重複を避け、整合性を保つための設計手法。

#### 第1正規形
- 各セルには1つの値のみ
- 繰り返しグループを排除

#### 第2正規形
- 主キー以外の列は主キー全体に依存

#### 第3正規形
- 推移的関数従属を排除

### 命名規則

```sql
-- テーブル名：複数形、スネークケース
customers, order_items, product_categories

-- 列名：単数形、スネークケース
customer_id, first_name, created_at

-- インデックス名：意味のある名前
idx_customer_email, idx_order_date_customer
```

## 今後調べたり勉強していくこと

- 基本的な文法とより複雑な文法をより詳しく習得
- データベース設計について
- SQLに関するセキュリティやアクセス制御
- パフォーマンス最適化
- python SQLAlchemy pandas
- PostgreSQLとMySQLの違い