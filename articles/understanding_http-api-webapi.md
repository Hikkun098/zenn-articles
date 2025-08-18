---
title: "HTTP/API/WEB APIの基礎を理解する"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["HTTP", "API", "WEBAPI"]
published: true
---
## 1. HTTPとは何か

HTTP（HyperText Transfer Protocol）は、インターネット上でデータをやり取りするための約束事（プロトコル）です。ウェブブラウザとウェブサーバー間の通信方法を定義しています。

### HTTPの特徴
- **クライアント-サーバーモデル**：リクエストを送る側（クライアント）と応答する側（サーバー）の関係
- **ステートレス**：各リクエストは独立しており、前後のリクエストの状態を覚えない
- **テキストベース**：人間が読める形式でデータをやり取り

## 2. HTTPリクエストの構造

HTTPリクエストは以下の要素で構成されています：

1. **リクエストライン**：HTTPメソッド + URL + HTTPバージョン
   ```
   GET /api/convert HTTP/1.1
   ```

2. **ヘッダー**：メタ情報（キーと値のペア）
   ```
   Host: api.example.com
   Content-Type: application/json
   Authorization: Bearer token123
   ```

3. **空行**：ヘッダーの終わりを示す

4. **ボディ**（省略可能）：送信するデータ
   ```json
   {
     "value": 10,
     "from_unit": "m",
     "to_unit": "cm"
   }
   ```

## 3. HTTPメソッド詳細

### GET
- **目的**：データの取得
- **特徴**：URLにパラメータを含める（クエリパラメータ）
- **例**：`GET /api/convert?value=10&from_unit=m&to_unit=cm`
- **単位変換での使用例**：変換結果を取得する

### POST
- **目的**：新しいデータの作成
- **特徴**：リクエストボディにデータを含める
- **例**：`POST /api/conversions` + ボディにデータ
- **単位変換での使用例**：変換履歴を保存する、複雑な変換リクエストを送信する

### PUT
- **目的**：既存データの完全な置き換え
- **特徴**：対象リソースの完全な状態をボディに含める
- **例**：`PUT /api/users/123` + ボディに全データ
- **単位変換での使用例**：ユーザー設定を丸ごと更新する

### PATCH
- **目的**：既存データの部分更新
- **特徴**：変更する部分だけをボディに含める
- **例**：`PATCH /api/users/123` + ボディに変更部分のみ
- **単位変換での使用例**：特定の変換設定のみを更新する

### DELETE
- **目的**：データの削除
- **特徴**：通常はボディなし
- **例**：`DELETE /api/conversions/123`
- **単位変換での使用例**：変換履歴を削除する

## 4. HTTPレスポンスの構造

レスポンスも同様に構造化されています：

1. **ステータスライン**：HTTPバージョン + ステータスコード + 説明
   ```
   HTTP/1.1 200 OK
   ```

2. **ヘッダー**：メタ情報
   ```
   Content-Type: application/json
   Content-Length: 82
   ```

3. **空行**

4. **ボディ**：返されるデータ
   ```json
   {
     "value": 10,
     "from_unit": "m",
     "to_unit": "cm",
     "result": 1000
   }
   ```

## 5. 主要なHTTPステータスコード

ステータスコードは3桁の数字で、レスポンスの状態を示します：

### 2xx（成功）
- **200 OK**：リクエスト成功
- **201 Created**：リソース作成成功
- **204 No Content**：成功したが返すコンテンツなし

### 3xx（リダイレクト）
- **301 Moved Permanently**：リソースが完全に移動
- **302 Found**：一時的なリダイレクト

### 4xx（クライアントエラー）
- **400 Bad Request**：リクエスト形式が不正
- **401 Unauthorized**：認証が必要
- **403 Forbidden**：権限がない
- **404 Not Found**：リソースが見つからない
- **422 Unprocessable Entity**：バリデーションエラー

### 5xx（サーバーエラー）
- **500 Internal Server Error**：サーバー内部エラー
- **503 Service Unavailable**：サーバーが一時的に利用不可

## 6. WEB APIとは

WEB APIとは、Webを通じて他のソフトウェアがアクセスできるようにしたアプリケーションの機能です。

### WEB APIの種類

1. **RESTful API**
   - リソース中心の設計
   - HTTPメソッドを使用
   - ステートレス（状態を保持しない）
   - 一般的にJSON形式でデータをやり取り

2. **SOAP API**
   - より厳格なプロトコル
   - XML形式でデータをやり取り
   - エンタープライズシステムで使用されることが多い

3. **GraphQL API**
   - クライアントが必要なデータだけを指定できる
   - 一度のリクエストで複数のリソースを取得可能
   - オーバーフェッチング・アンダーフェッチングの問題を解決

## 7. APIのエンドポイント設計

良いAPIエンドポイントの設計原則：

1. **リソース中心に考える**
   - 名詞を使う：`/conversions` （良い）vs `/convert` （あまり良くない）
   - 複数形を使う：`/users` （良い）vs `/user` （あまり良くない）

2. **階層構造を表現**
   - `/users/123/conversions`：ユーザー123の変換履歴

3. **クエリパラメータの使い方**
   - フィルタリング：`/conversions?type=length`
   - ソート：`/conversions?sort=date`
   - ページネーション：`/conversions?page=2&per_page=10`

4. **一貫性を保つ**
   - 命名規則を統一する
   - レスポンス形式を統一する

## 8. JSON（JavaScript Object Notation）

JSONは、WEB APIでデータをやり取りする際の標準的な形式です。

### 特徴
- 軽量
- 人間が読み書きしやすい
- 機械が解析・生成しやすい
- 言語に依存しない

### 基本的な構造
```json
{
  "string": "これは文字列です",
  "number": 42,
  "boolean": true,
  "null": null,
  "array": [1, 2, 3],
  "object": {
    "key": "value"
  }
}
```

## 9. 単位変換APIの実際のHTTPやり取り例

### 例1：GETリクエストでの単位変換

**リクエスト**
```
GET /api/convert?value=10&from_unit=m&to_unit=cm HTTP/1.1
Host: api.example.com
```

**レスポンス**
```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "value": 10,
  "from_unit": "m",
  "to_unit": "cm",
  "result": 1000
}
```

### 例2：POSTリクエストでの単位変換

**リクエスト**
```
POST /api/conversions HTTP/1.1
Host: api.example.com
Content-Type: application/json

{
  "value": 10,
  "from_unit": "m",
  "to_unit": "cm"
}
```

**レスポンス**
```
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/conversions/456

{
  "id": 456,
  "value": 10,
  "from_unit": "m",
  "to_unit": "cm",
  "result": 1000,
  "created_at": "2025-05-12T10:30:00Z"
}
```

### 例3：エラーの場合

**リクエスト**
```
GET /api/convert?value=10&from_unit=m&to_unit=unknown HTTP/1.1
Host: api.example.com
```

**レスポンス**
```
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": "サポートされていない単位です",
  "detail": "to_unit 'unknown' は有効な単位ではありません"
}
```

## 10. API開発で覚えておきたい重要な概念

1. **認証と認可**
   - **認証（Authentication）**：ユーザーが誰であるかを確認する
   - **認可（Authorization）**：ユーザーが何をできるかを決定する
   - よく使われる方法：API キー、JWT（JSON Web Token）、OAuth

2. **レート制限**
   - APIへのリクエスト回数を制限する仕組み
   - 過負荷やDoS攻撃から保護する

3. **CORS（Cross-Origin Resource Sharing）**
   - ブラウザにおける同一オリジンポリシーの制限を緩和する仕組み
   - 異なるドメインからのAPIアクセスを制御する

4. **バージョニング**
   - API変更時の互換性維持のための戦略
   - 例：`/api/v1/convert`、`/api/v2/convert`
