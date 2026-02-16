# 商品詳細機能

## 機能概要
<a id="機能概要"></a>

| 項目 | 内容 |
|------|------|
| モジュールID | PRODUCT_DETAIL_001 |
| モジュール名 | 商品詳細機能 |
| 作成日 | 2026-02-16 |
| 最終更新日 | 2026-02-16 |

URLパラメータから商品IDを取得し、商品詳細情報をAPIから取得して表示する機能。カートへの商品追加機能も提供する。

## 目次
<a id="目次"></a>

- [機能概要](#機能概要)
- [Reactのライフサイクルフック](#reactのライフサイクルフック)
- [Reactイベント](#reactイベント)
- [関数仕様](#関数仕様)
  - [handleAddToCart](#handleaddtocart)
  - [fetch](#fetch)
- [API呼び出し仕様](#api呼び出し仕様)
  - [productApi.getProduct](#productapi.getproduct)
  - [cartApi.addItem](#cartapi.additem)
- [型定義](#型定義)
- [変更履歴](#変更履歴)

---

## Reactのライフサイクルフック
<a id="reactのライフサイクルフック"></a>

<details open>
<summary><strong>useEffect (fetchProduct)</strong></summary>

### 概要
URLパラメータから商品IDを取得し、商品詳細情報をAPIから取得してstateに設定

### 入力パラメータ

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | id | string \| undefined | - | useParamsで取得されたURLパラメータ |

### 出力パラメータ

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| state | product | Product \| null | - | 商品情報 |
| state | isLoading | boolean | - | ローディング状態 |
| state | error | string \| null | - | エラーメッセージ |

### 処理フロー

1. idパラメータの存在確認（なければ処理を中断）
2. isLoadingをtrueに設定
3. [`productApi.getProduct`](#productapi.getproduct)(id)でAPIを呼び出し
4. 成功時: data.dataをproductステートに設定
5. エラー時: エラーメッセージをerrorステートに設定
6. finally: isLoadingをfalseに設定

### 呼び出す関数

- [`productApi.getProduct`](#productapi.getproduct)

</details>

---

## Reactイベント
<a id="reactイベント"></a>

<details open>
<summary><strong>イベント一覧</strong></summary>

### カートに追加ボタンクリック

| 項目 | 内容 |
|------|------|
| トリガー | Button onClick ([`handleAddToCart`](#handleaddtocart)) |
| パネル名 | 商品詳細画面 |
| 概要 | 認証チェック後、商品をカートに追加してカート画面に遷移 |

**呼び出す関数:**
- [`handleAddToCart`](#handleaddtocart)

**API呼び出し:**
- [`cartApi.addItem`](#cartapi.additem)

---

### 戻るボタンクリック

| 項目 | 内容 |
|------|------|
| トリガー | Button onClick ([`navigate`](#navigate)(-1)) |
| パネル名 | 商品詳細画面 |
| 概要 | 前の画面に戻る |

**呼び出す関数:**
- [`navigate`](#navigate)

**API呼び出し:**
なし

</details>

---

## 関数仕様
<a id="関数仕様"></a>

<a id="handleaddtocart"></a>
<details open>
<summary><strong>handleAddToCart</strong></summary>

### 概要
商品をカートに追加する処理。未認証の場合はログイン画面に遷移、成功時はカート画面に遷移

### 入力パラメータ

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| closure | product | Product \| null | - | useStateで管理されているステート |
| closure | isAuthenticated | boolean | - | useAuthフックから取得 |

### 出力パラメータ

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| state | addingToCart | boolean | - | カート追加処理中フラグ |
| state | error | string \| null | - | エラーメッセージ |
| navigation | navigate | void | - | ナビゲーション |

### 処理フロー

1. productの存在確認（なければ処理を中断）
2. isAuthenticatedをチェック、未認証の場合は/loginに遷移
3. addingToCartをtrueに設定
4. [`cartApi.addItem`](#cartapi.additem)(product.id, 1)でAPIを呼び出し
5. 成功時: /cartに遷移
6. エラー時: エラーメッセージをerrorステートに設定
7. finally: addingToCartをfalseに設定

</details>

<a id="fetch"></a>
<details open>
<summary><strong>fetch (useEffect内)</strong></summary>

### 概要
商品詳細情報をAPIから取得する非同期関数

### 入力パラメータ

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| closure | id | string | - | useParamsで取得されたURLパラメータ |

### 出力パラメータ

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| state | product | Product \| null | - | 商品情報 |
| state | isLoading | boolean | - | ローディング状態 |
| state | error | string \| null | - | エラーメッセージ |

### 処理フロー

1. isLoadingをtrueに設定
2. [`productApi.getProduct`](#productapi.getproduct)(id)を呼び出し
3. 成功時: レスポンスのdata.dataをproductステートに設定
4. エラー時: ApiErrorResponseからエラーメッセージを抽出し、errorステートに設定
5. finally: isLoadingをfalseに設定

</details>

---

## API呼び出し仕様
<a id="api呼び出し仕様"></a>

<a id="productapi.getproduct"></a>
<details open>
<summary><strong>productApi.getProduct</strong></summary>

### 概要
商品詳細情報を取得

### エンドポイント情報

| 項目 | 内容 |
|------|------|
| エンドポイント | /products/:id |
| メソッド | GET |

### リクエストパラメータ

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | id | string | ✓ | 商品ID（パスパラメータ） |

**型定義:** `string`

### レスポンス

**型定義:** `ApiResponse<Product>`

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | data | Product | ✓ | 商品情報 |
| root | message | string | - | メッセージ |

<details>
<summary>Product 型の詳細</summary>

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | id | string | ✓ | 商品ID |
| root | name | string | ✓ | 商品名 |
| root | description | string | ✓ | 商品説明 |
| root | price | number | ✓ | 価格 |
| root | imageUrl | string | ✓ | 商品画像URL |
| root | categoryId | string | ✓ | カテゴリID |
| root | stock | number | ✓ | 在庫数 |
| root | createdAt | string | ✓ | 作成日時 |
| root | updatedAt | string | ✓ | 更新日時 |

</details>

### エラーハンドリング
ApiErrorResponseをcatchし、error messageをステートに設定

</details>

<a id="cartapi.additem"></a>
<details open>
<summary><strong>cartApi.addItem</strong></summary>

### 概要
商品をカートに追加

### エンドポイント情報

| 項目 | 内容 |
|------|------|
| エンドポイント | /cart/items |
| メソッド | POST |

### リクエストパラメータ

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | productId | string | ✓ | カートに追加する商品ID |
| root | quantity | number | ✓ | 追加する数量 |

**型定義:** `{ productId: string, quantity: number }`

### レスポンス

**型定義:** `ApiResponse<Cart>`

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | data | Cart | ✓ | カート情報 |
| root | message | string | - | メッセージ |

<details>
<summary>Cart 型の詳細</summary>

| 階層 | パラメータ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | items | CartItem[] | ✓ | カート内の商品リスト |
| root | totalPrice | number | ✓ | 合計金額 |

</details>

### エラーハンドリング
ApiErrorResponseをcatchし、error messageをステートに設定

</details>

---

## 型定義
<a id="型定義"></a>

<details open>
<summary><strong>インターフェース一覧</strong></summary>

### Product

**説明:** 商品情報を表すインターフェース

| 階層 | プロパティ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | id | string | ✓ | 商品ID |
| root | name | string | ✓ | 商品名 |
| root | description | string | ✓ | 商品説明 |
| root | price | number | ✓ | 価格 |
| root | imageUrl | string | ✓ | 商品画像URL |
| root | categoryId | string | ✓ | カテゴリID |
| root | stock | number | ✓ | 在庫数 |
| root | createdAt | string | ✓ | 作成日時 |
| root | updatedAt | string | ✓ | 更新日時 |

---

### Cart

**説明:** カート情報を表すインターフェース

| 階層 | プロパティ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | items | CartItem[] | ✓ | カート内の商品リスト |
| root | totalPrice | number | ✓ | 合計金額 |

---

### ApiResponse<T>

**説明:** 汎用APIレスポンス型（ジェネリック）

| 階層 | プロパティ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | data | T | ✓ | レスポンスデータ |
| root | message | string | - | メッセージ |

---

### ApiErrorResponse

**説明:** APIエラーレスポンス

| 階層 | プロパティ名 | 型 | 必須 | 説明 |
|------|-------------|-----|------|------|
| root | status | number | ✓ | HTTPステータスコード |
| root | message | string | ✓ | エラーメッセージ |
| root | errors | Record<string, string[]> | - | バリデーションエラー詳細 |

</details>

---

## 変更履歴
<a id="変更履歴"></a>

| 日付 | バージョン | 変更内容 | 変更者 |
|------|-----------|---------|--------|
| 2026-02-16 | 1.0.0 | 初版作成 | - |
