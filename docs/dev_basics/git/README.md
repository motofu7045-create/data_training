# git の使い方

このリポジトリを自分の Mac で実際に操作しながら学ぶ。

## 学習の流れ

| 段階 | やること | 状況 |
| --- | --- | --- |
| 0 | 準備（Git の確認と初期設定） | 完了 |
| 0.5 | SSH 鍵の作り直し → [01_ssh_key_setup.md](01_ssh_key_setup.md) | 完了（2026-10-02） |
| 1 | GitHub の最新を取り込む（fetch と pull） | 次にやる |
| 2 | 基本の流れ: 変更 → add → commit → push | |
| 3 | ブランチを切って、プルリクエストを出してマージする | |
| 4 | わざとコンフリクト（衝突）を起こして解決する | |
| 5 | 間違えたときの戻し方 | |

## 全体像

ファイルの変更は4つの場所を順に移っていく。

```
作業フォルダ  →  ステージ  →  ローカルの履歴  →  GitHub（リモート）
（編集する）   git add      git commit         git push
                                   ←──────────────── git pull
```

| コマンド | 意味 |
| --- | --- |
| `git add` | この変更を次の記録に入れると選ぶ |
| `git commit` | 選んだ変更を、メッセージ付きで自分の PC の履歴に記録する |
| `git push` | 自分の PC の履歴を GitHub に送る |
| `git fetch` | GitHub の最新の履歴を取ってくるだけ（手元のファイルは変わらない） |
| `git pull` | fetch ＋ 取り込み（merge）をまとめて行う |

## 状態を確認するコマンド

| コマンド | 何が分かるか |
| --- | --- |
| `pwd` | 今いるフォルダの場所 |
| `git remote -v` | このフォルダがどの GitHub リポジトリとつながっているか |
| `git branch` | 今いるブランチ（`*` が付いているのが今のブランチ） |
| `git status` | 手元に未保存の変更があるか、GitHub と比べて遅れているか |
| `git log --oneline -5` | 直近5件の記録（コミット） |

## 学んだこと

### `git status` の「up to date（最新）」は古い情報のことがある

- `origin/main` は「GitHub の main を**最後に確認したとき**の記録」にすぎない
- **Git は自分から GitHub を見に行かない**。`git fetch` するまで、手元の `origin/main` は古いまま
- 本当に最新かを知るには、先に `git fetch` してから `git status` を見る

### 接続方法: SSH

- `git remote -v` で `git@github.com:...` と出たら **SSH** で GitHub とつながっている
- SSH の鍵が使えないと、fetch・pull・push のときに `Permission denied (publickey)` が出る → [01_ssh_key_setup.md](01_ssh_key_setup.md)
