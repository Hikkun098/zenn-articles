---
title: "【Python】非同期処理(async/await)を理解する" # 記事のタイトル
emoji: "📝" # アイキャッチとして使用する絵文字
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["python"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---

## 【Python】非同期処理(async/await)を理解する

作成中〜
これは複雑で難しいので今後修正したり追記する予定

## Step 1：非同期の必要性を理解しよう

非同期処理とは、ある処理の完了を待たずに次の処理を実行する方式のことです。
例えば
Webサイトで画像やデータを読み込む際に、読み込みが完了するまで他の操作ができないと、ユーザーは不便を感じます。
非同期処理を使うことで、読み込み中に他のボタンをクリックしたり、スクロールしたりできるようになります。﻿
これにより、処理の効率化やユーザー体験の向上が期待できます。﻿

### 現実世界の例：コンビニの店員さん
```
【従来の店員さん（同期）】
お客さん1: レジ会計 → 完全に終わるまで待つ
お客さん2: やっと順番 → また完全に終わるまで待つ
お客さん3: やっと順番...

結果：お客さんが行列で怒ってる😡
```

```
【上手な店員さん（非同期）】
お客さん1: 「揚げ物温めますね」→ 温めてる間に
お客さん2: 「こちらのレジどうぞ」→ 会計しながら
お客さん1: 「温まりました！」→ 渡す

結果：みんなスムーズ😊
```

**これが非同期の基本アイデアです！**

---

## Step 2: プログラムでも同じことが起きる

### 問題：ウェブサイトが遅い
```
ユーザーがボタンを押す
↓
「データベースから情報取得中...」（3秒）
↓
この間、画面が固まって何もできない😰
↓
やっと結果表示
```

### 解決：非同期処理
```
ユーザーがボタンを押す
↓
「データベースから情報取得中...」（裏で処理）
↓
この間も、他のボタンは押せる😊
↓
取得完了したら結果表示
```


## Step 3：最初の最小コード

```python
import asyncio

async def say_hello():
    return "Hello"

async def main():
    result = await say_hello()
    print(result)

asyncio.run(main())
```

**実行結果**: `Hello`

---

## 最小限のコード例2: 待つ処理

```python
import asyncio

async def fetch_data():
    print("Start fetching...")
    await asyncio.sleep(2)
    print("Fetching done!")
    return "data"

async def main():
    result = await fetch_data()
    print(result)

asyncio.run(main())
```

**実行結果**:
```
Start fetching...
(2秒後)
Fetching done!
data
```
## Step 4：awaitの意味を体験

```python
import asyncio

async def fetch_user():
    print("Start fetching user...")
    await asyncio.sleep(1)
    print("User fetched!")
    return {"id": 1, "name": "john"}

async def main():
    user = await fetch_user()
    print(user)

asyncio.run(main())
```

**実行結果**:
```
Start fetching user...
(1秒後)
User fetched!
{'id': 1, 'name': 'john'}
```

**`await` = 「この処理の完了を待って」という意味**

---

## Step 5：awaitを忘れたらどうなる？

```python
import asyncio

async def fetch_user():
    await asyncio.sleep(1)
    return {"id": 1, "name": "john"}

async def main():
    user = fetch_user()  # ❌ await忘れ
    print(user)

asyncio.run(main())
```

**実行結果**:
```python
# 変な文字が出力される
<coroutine object fetch_user at 0x...>
```

---

## Step 6：正しくawaitをつける

```python
import asyncio

async def fetch_user():
    await asyncio.sleep(1)
    return {"id": 1, "name": "john"}

async def main():
    user = await fetch_user()  # ✅ awaitつけた
    print(user)

asyncio.run(main())
```

**実行結果**:
```
{'id': 1, 'name': 'john'}
```




