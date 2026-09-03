# AGENTS.md — つむぎん作品リポジトリの執筆ガイド

このリポジトリは、物語プラットフォーム「つむぎん」（https://tsumugin.com ）で公開する作品のリポジトリです。
AIエージェントはこのファイルの規約に従って本文・設定資料を書いてください。規約に沿っていれば、`git push` するだけで公開されます。

## 作業の基本

- 本文は `episodes/` の Markdown に直接書く。エディタ専用のデータやビルド生成物は作らない
- 書き終えたら `npx tsumugin@latest validate` を実行し、エラーが 0 になってからコミットする
- 人が読んで判断するのは `npx tsumugin@latest editor`（縦書きプレビュー）。ファイルを保存すると開いているプレビューに自動で反映される
- 公開するかどうかは frontmatter の `published` で決まる。**指示がない限り `published: true` に変えない**（push した時点で公開されるため）
- `story.yaml` の `slug`、話のファイル名の番号は公開URLに使われる。**一度公開したら変えない**

## ディレクトリ構成

```
<作品ディレクトリ>/
├── story.yaml            # 作品メタデータ（必須）
├── episodes/             # 話（必須）
│   ├── 001-prologue.md          # ファイル形式
│   └── 002-first-night/         # ディレクトリ形式
│       ├── index.md             #   本文はここ
│       └── images/              #   画像や構成メモを同居できる（index.md 以外は無視）
├── characters/           # キャラクター設定（任意）
├── world/                # 世界観・用語集（任意）
└── assets/               # 表紙・挿絵（任意）
```

- `story.yaml` があるディレクトリが1作品。`README.md`・`AGENTS.md`・`.github/` など規約外のファイルは自由に置ける（ビルド対象外）
- 1リポジトリに複数の作品を置く場合はサブディレクトリごとに `story.yaml` を置く。作品の入れ子は禁止

## story.yaml

```yaml
title: 月影の図書館            # 必須（1〜100文字）
slug: yoru-no-toshokan        # 任意。公開URLの一部（[a-z0-9-]+）。既定はディレクトリ名
summary: |                    # 推奨（最大2000文字）。作品ページのあらすじ
  夜にだけ開く図書館に迷い込んだ少女の物語。
genre: ファンタジー            # 任意
status: ongoing               # 任意。ongoing（連載中）/ completed（完結）
tags: [ファンタジー, 連載中]   # 任意（最大10個、各1〜30文字）
cover: assets/cover.png       # 任意。作品ディレクトリからの相対パス
comments: true                # 任意（既定: true）
ai_disclosure: "文章: Claude / 挿絵: codex"  # 推奨（最大200文字）。AI利用の明示
language: ja                  # 任意（既定: ja）
```

## 話（episodes/）

### ファイル名

```
episodes/NNN-slug.md            # 例: 001-prologue.md
episodes/NNN-slug/index.md      # 例: 012-the-long-night/index.md
```

- `NNN` は3桁ゼロ埋めの話番号。**表示順はこの番号で決まる**。重複禁止
- `slug` は半角英小文字・数字・ハイフンのみ
- 新しい話は `npx tsumugin@latest new --slug <slug> --title "<タイトル>"` で作ると、次の番号が自動で振られる

### frontmatter

```markdown
---
title: 星降る夜に                          # 必須。「第3話」などの話数は含めない
published: false                           # 任意（既定: false = 下書き）
publish_at: 2026-09-01T20:00:00+09:00      # 任意。published: true かつ未来なら予約公開
chapter: 第一章 出会い                      # 任意。同じ章名の話が目次でまとまる
---

本文……
```

| `published` | `publish_at` | 状態 |
| :-: | :-: | --- |
| `false`（省略） | — | 下書き（公開されない） |
| `true` | なし | 公開 |
| `true` | 未来 | 予約公開（到来で自動公開） |
| `true` | 過去 | 公開（公開日時として表示） |

## 本文の記法

CommonMark（一般的な Markdown）に、物語向けの記法を足したものです。

| 書きたいもの | 書き方 | 備考 |
| --- | --- | --- |
| ルビ（明示） | `\|親文字《るび》` | 親文字に任意の文字列を使える。`\|` は全角でも可 |
| ルビ（省略形） | `月影《つきかげ》` | `《` の直前の漢字連続が親文字になる |
| 傍点 | `《《ぞわり》》` | 囲んだ文字に一文字ずつ傍点 |
| 縦中横 | 自動 | 半角数字1〜3桁（`12月`）や `!?` は縦書き時に横並びになる |
| 挿絵 | `![夜の図書館の外観](../assets/ep001-01.png)` | 角括弧内はキャプションとして画像の下に表示される |
| 表 | GFM テーブル | 設定資料向け。縦書きでも表は横書きのまま |
| エスケープ | `\|` `\《` `\》` | 記法として解釈させたくないとき |

- 段落は空行で区切る。読者は縦書きで読むことが多いので、1段落を長くしすぎない
- HTML タグは書かない（サニタイズされる）。脚注などの拡張記法は未対応
- 挿絵の参照先は作品ディレクトリ内に実在するファイルにする（`validate` が検出する）

## 設定資料（characters/ と world/）

ファイル名規約は話と同じ `NNN-slug.md`（番号が表示順）。

```markdown
---
name: 灯里（あかり）      # characters は name、world は title（必須）
public: false            # 既定: false（非公開）
---

夜の図書館に迷い込む少女。……
```

- **既定は非公開**。ネタバレを含む設定を AI 執筆のコンテキストとして自由に書いてよい
- 読者に見せたいものだけ `public: true` にする。非公開の資料は公開サイトに送られない

## 画像（assets/）

- 形式: PNG / JPEG / WebP / AVIF。1ファイル最大 5MB。配信時に自動で最適化されるので変換は不要
- **表紙は 2:3 の縦長（推奨 1200×1800、最低 600×900、500KB 以下）**。公開サイトは全画面で 2:3 の枠に表示し、比率が違う表紙は切り抜かずに枠へ収める（余白はぼかしで埋まる）ので、2:3 で作るのが最も映える。主要な要素は上下左右 5% の内側に置く
- 挿絵は 3:2 か 16:9 を推奨。長辺 1600px 程度（最低 800px）、1ファイル 1MB 以下を目安に
- AI 生成の PNG は大きくなりがちなので、**JPEG / WebP に変換して置く**
- 表紙は `story.yaml` の `cover` で指定。挿絵は本文から相対パスで参照する
- 画像は話のディレクトリ形式で `images/` に同居させてもよい

## 公開までの流れ

1. 書く → `npx tsumugin@latest validate` でエラー 0 を確認
2. 人が `npx tsumugin@latest editor` で読んで判断する
3. 公開する話の frontmatter を `published: true` にする（予約なら `publish_at` も）
4. `git push`。つむぎんと連携済みのリポジトリなら数十秒で公開される。ビルド結果はコミットの `tsumugin/build` ステータスとダッシュボードに出る

つむぎんとの連携手順（サインイン → GitHub App のインストール → 連携ON）は
https://github.com/hampen2929/tsumugin-story-template#readme を参照してください。
