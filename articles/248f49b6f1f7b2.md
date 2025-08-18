---
title: "GitHubでSSH認証設定方法"
emoji: "🤯"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Github", "SSH"]
published: true
---

# GitHubでSSH認証設定方法

## 問題の発生

今まで個人開発ではHTTPS認証で問題なく使えていたのですが、
Zennの記事を投稿しようと `git push` したところ、突然以下のエラーが発生😭

```bash
Password for 'https://ghp_xxxxxxxx@github.com': 
remote: Invalid username or token. Password authentication is not supported for Git operations.
fatal: Authentication failed for 'https://github.com/[username]/zenn-articles.git/'
```

**原因分析**: エラーメッセージの `ghp_` で始まる文字列はPersonal Access Token（PAT）。
今回の真の原因は**PATの期限切れ**でした。
個人開発プロジェクトでは問題なくpushできていたため、プロジェクトごとに異なるPATまたは期限の違いが原因と推測されます。

## 解決方法の選択肢

実は**2つの解決方法**がありました。

### 方法A: HTTPS認証を継続
1. 新しいPATを作成
2. 古いクレデンシャルを削除
3. 新しいPATでpush

### 方法B: SSH認証に移行

- トークンの期限管理が不要
- GitHubの推奨方式
- 一度設定すれば永続的に使用可能

この記事では**推奨されているSSH認証方式**を採用しました。

### 1. 現在のSSH設定確認

```bash
ls ~/.ssh
```

SSH鍵がない場合は以下のファイルのみ表示される
- `known_hosts` ← 過去にSSH接続したサーバーの記録（セキュリティ機能）
- `known_hosts.old` ← known_hostsの古いバックアップ

**注意**: これらのファイルは過去にSSH接続した証拠。
　　　削除しても問題ないが、セキュリティ機能として有用なのでそのまま残すことを推奨。

### 2. SSH鍵の作成

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

**`ed25519`について**: 楕円曲線デジタル署名アルゴリズムの一種。RSAより高速・安全・コンパクトでGitHubでも推奨されている暗号化方式。

- **ファイルの保存場所**: そのままEnterを押す（デフォルトでOK）
- **パスフレーズ**: セキュリティのため設定推奨
- **パスフレーズ確認**: 同じものを再入力

### 3. SSH鍵の確認

```bash
ls ~/.ssh
```

以下のファイルが作成されていることを確認
- `id_ed25519` ← 秘密鍵（絶対に他人に見せない）
- `id_ed25519.pub` ← 公開鍵（GitHubに登録する）

### 4. 公開鍵をGitHubに登録

#### 4-1. 公開鍵をコピー
```bash
cat ~/.ssh/id_ed25519.pub
```

表示される内容（`ssh-ed25519 AAAA...`で始まる長い文字列）を全てコピー

#### 4-2. GitHubでの設定
1. GitHub.com にログイン
2. 右上のプロフィール画像 → **Settings**
3. 左側メニューの **SSH and GPG keys**
4. **New SSH key** をクリック
5. **Title**: 識別しやすい名前
6. **Key**: コピーした公開鍵を貼り付け
7. **Add SSH key** をクリック

サンプル画像
青色の`NEW SSH KEY`ボタンになります。
![GitHubのSSH keys設定画面](/images/github-ssh-keys.png)

### 5. SSH接続テスト

```bash
ssh -T git@github.com
```

初回は以下の確認が表示される
```bash
The authenticity of host 'github.com' can't be established.
...
Are you sure you want to continue connecting (yes/no/[fingerprint])? 
```

**`yes`** と入力してEnter

成功すると以下が表示
```bash
Hi [username]! You've successfully authenticated, but GitHub does not provide shell access.
```

### 6. リモートURLをSSHに変更

```bash
# 現在の設定確認
git remote -v

# HTTPSからSSHに変更
git remote set-url origin git@github.com:[username]/[repository].git

# 変更確認
git remote -v
```

### 7. pushテスト

```bash
git push origin main
```

## 発生した問題：毎回パスフレーズを求められる

pushは成功するが、毎回SSHのパスフレーズ入力を求められるように…🫠

### 解決方法：SSH-agentでパスフレーズを記憶

```bash
# MacのキーチェーンにSSH鍵を追加
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

これで今後はパスフレーズの入力が不要になる😆

### 確認方法

```bash
# 読み込まれているSSH鍵の確認
ssh-add -l

# pushテスト（パスフレーズなしで成功するはず）
git push origin main
```

## コマンドの意味

### `ssh-add --apple-use-keychain ~/.ssh/id_ed25519`

| 部分 | 意味 |
|------|------|
| `ssh-add` | SSH鍵をメモリに読み込むコマンド |
| `--apple-use-keychain` | macOSのキーチェーン機能を使用 |
| `~/.ssh/id_ed25519` | 作成したSSH秘密鍵のパス |

**安全性**: すべてローカルマシン内で完結し、外部への情報送信はなし

## 元に戻す方法（必要な場合）

```bash
# SSH鍵をメモリから削除
ssh-add -D

# 確認
ssh-add -l
# → "The agent has no identities." と表示されればOK
```

## まとめ

1. **GitHubはパスワード認証を廃止**したため、SSH認証が必要
2. **SSH鍵は一度設定すれば全プロジェクトで使用可能**
3. **既存プロジェクトへの影響はなし**（個別に変更可能）
4. **セキュリティが向上**し、使い勝手も良くなる

## 追加メモ

- SSH鍵は**コンピュータ全体の設定**なので、どのディレクトリで作業しても同じ
- **公開鍵**は人に見せても安全、**秘密鍵**は絶対に秘匿
- 他のGitプロジェクトも同様にSSHに変更可能（任意）

---

*この記事は実際にトラブルシューティングを行った経験をもとに作成しました。*