# SSH 鍵の作り直し（Mac）

2026-10-02 に実施。古い鍵のパスフレーズを忘れて GitHub につながらなくなったため、新しい鍵を作って登録し直した。

## 起きたこと

`git fetch` を実行したら、次のように表示されて止まった。

```
Enter passphrase for key '/Users/（ユーザー名）/.ssh/id_ed25519':
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

- `Enter passphrase for key` は、Mac にある SSH 鍵の**パスフレーズ**（鍵を作ったときに自分で決めた合言葉）を聞いている。**GitHub のパスワードとは別物**
- `Permission denied (publickey)` は、GitHub が「その鍵ではログインを認めない」と返したということ

考えられる原因は2つ。

| 原因 | よくある状況 |
| --- | --- |
| パスフレーズが違った（または Enter だけ押した） | 入力しても画面に何も表示されないので、打ち間違いに気づきにくい |
| 鍵が GitHub アカウントに登録されていない | 鍵を作り直した、別のアカウントに登録した、など |

パスフレーズを入力しているあいだ画面に何も表示されないのは**正常**（セキュリティのため）。

## 原因の切り分け方

```bash
# GitHub へのログインだけを試す
ssh -T git@github.com

# 鍵の指紋（SHA256:...）を表示する。GitHub の Settings → SSH and GPG keys の一覧と見比べる
ssh-keygen -lf ~/.ssh/id_ed25519.pub
```

## SSH 鍵のしくみ

鍵は2つで1組。

| ファイル | 中身 | 扱い |
| --- | --- | --- |
| `id_ed25519` | **秘密鍵** | 自分の PC から**絶対に出さない**（人に見せない、貼り付けない） |
| `id_ed25519.pub` | **公開鍵** | GitHub に登録する。人に見られても問題ない |

GitHub は「登録された公開鍵とペアになる秘密鍵を持っているか」を確かめてログインを認める。

## 作り直しの手順

### 1. 古い鍵を退避する（消さずに名前を変える）

```bash
mv ~/.ssh/id_ed25519 ~/.ssh/id_ed25519.old
mv ~/.ssh/id_ed25519.pub ~/.ssh/id_ed25519.pub.old
```

### 2. 新しい鍵を作る

```bash
ssh-keygen -t ed25519 -C "GitHubに登録しているメールアドレス"
```

- `-t ed25519`: 鍵の種類。今の標準
- `-C`: 鍵に付けるメモ（コメント）
- 保存場所を聞かれたら何も入力せず Enter（標準の場所 `~/.ssh/id_ed25519`）
- パスフレーズを2回入力する。**パスワード管理アプリに保存しておく**

### 3. Mac にパスフレーズを覚えさせる

設定ファイル `~/.ssh/config` に次の内容を書く（すでにファイルがある場合は、中身を確認してから追記する）。

```
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

| 設定 | 意味 |
| --- | --- |
| `AddKeysToAgent yes` | 鍵を使うときに自動で ssh-agent（鍵を預かるプログラム）に登録する |
| `UseKeychain yes` | パスフレーズを Mac のキーチェーンに保存して、毎回聞かれないようにする |
| `IdentityFile` | GitHub につなぐときに使う鍵の場所 |

続けて、鍵をキーチェーンに登録する（ここでパスフレーズを一度だけ聞かれる）。

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

### 4. 公開鍵を GitHub に登録する

```bash
# 公開鍵（.pub）をクリップボードにコピーする
pbcopy < ~/.ssh/id_ed25519.pub
```

1. GitHub 右上のアイコン →「Settings」→ 左メニューの「SSH and GPG keys」
2. 「New SSH key」
3. Title: 分かる名前（例: `MacBook Air 2026-10`）、Key type: Authentication Key、Key: ⌘+V で貼り付け
4. 「Add SSH key」
5. 使わなくなった古い鍵が一覧にあれば「Delete」

### 5. つながるか試す

```bash
ssh -T git@github.com
```

- 初めてつなぐときは `Are you sure you want to continue connecting (yes/no)?` と聞かれる。表示された指紋が GitHub 公式の指紋 `SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU` と同じなら `yes`
- `Hi （GitHubのユーザー名）! You've successfully authenticated` と出れば成功

## 覚えておくこと

- `Permission denied (publickey)` が出たら、まず `ssh -T git@github.com` で GitHub へのログインだけを試して、Git の問題か鍵の問題かを切り分ける
- 秘密鍵（`.pub` が付いていない方）は誰にも渡さない。GitHub に登録するのは公開鍵（`.pub`）
- パスフレーズはパスワード管理アプリに保存し、Mac のキーチェーンにも覚えさせておく
- 新しい PC を使うときは、その PC で鍵を作って GitHub に登録する（PC ごとに鍵を分けるのが一般的）
