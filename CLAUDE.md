# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

fieldside.net のコーポレートサイト。Hugo静的サイトジェネレーターで構築。

- **本番URL**: https://www.fieldside.net
- **テーマ**: [Tella](https://github.com/opera7133/tella) (Tailwind CSSベース)
- **言語**: 日本語 (ja-jp)

## ブランチ構成

| ブランチ | 用途 |
|---------|------|
| `main` | GitHub Pages公開ブランチ（ビルド済みHTML） |
| `blog` | Hugoプロジェクトのソースブランチ |

**重要**: `main`ブランチを直接編集しないこと。`blog`ブランチで作業し、`make deploy`でビルド結果を`main`にデプロイする。

## 開発コマンド

```bash
# ローカルサーバー起動（ドラフト含む、ホットリロード有効）
make server   # または make s

# 新規記事作成（content/post/YYYY/MM/DD/配下に生成、ブランチも自動作成）
make new      # または make n

# 記事編集（pecoで記事選択 → nvimで編集）
make edit     # または make e

# デプロイ（hugo build → mainブランチへpush）
# .envにGITHUB_PATが必要
make deploy
```

## デプロイフロー

`make deploy`の処理:
1. `hugo`でビルド → `public/`に出力
2. `CNAME`ファイルを`public/`にコピー
3. `tmp/`にmainブランチをclone
4. `public/`の内容をclone先に同期
5. mainブランチにcommit & push
6. `tmp/`を削除

## アーキテクチャ

### テーマカスタマイズ

`themes/tella/`はgit submodule（ローカルでは空の場合あり）。テーマの一部をプロジェクト側でオーバーライドしている:

- `layouts/index.html` - トップページ（Swiperカルーセル + サービス紹介）
- `layouts/partials/header.html` - ナビゲーションバー
- `layouts/partials/footer.html` - フッター（JS読み込み）
- `layouts/partials/strip.html` - サービス紹介セクション（2カテゴリ）

### データ駆動コンテンツ

`data/`ディレクトリのJSONファイルでコンテンツを管理:

- `data/features.json` - サービス一覧（販売・せどり / システム開発）
- `data/slide.json` - トップページカルーセルのスライド

テンプレート内で`$.Site.Data.features`や`$.Site.Data.slide`として参照される。

### 使用ライブラリ（CDN読み込み）

- Swiper - カルーセル/スライダー
- Highlight.js - コードハイライト
- Clipboard.js - コピー機能
- SweetAlert2 - 通知

### コンテンツ構成

- `content/about.md` - 会社情報ページ
- `content/contact.md` - お問い合わせ（Google Formsのiframe埋め込み）
- ブログ記事は `content/post/YYYY/MM/DD/` 配下

### カスタムショートコード

- `layouts/shortcodes/table.html` - テーブル表示用ショートコード

## 注意事項

- テーマsubmoduleが空の場合、`git submodule update --init`でテーマを取得する必要がある
- `config.toml`の`markup.goldmark.renderer.unsafe = true`により、コンテンツ内のHTMLがそのままレンダリングされる
- カスタムCSSは`static/css/custom.css`に記述（レスポンシブテーブルスタイル等）
- 画像は`static/img/`配下に配置（logo/、slide/、strip/）
