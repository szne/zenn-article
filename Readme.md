# zenn-article

## 必要なもの
- node.js
- github cli

## 使い方

```fish
gh repo clone szne/zenn-article
npx zenn preview
```

## コミットメッセージ規則

### articles, books

フォーマット

```
<type>: <scope>
<BLANK LINE>
<body>
```

#### Type

\<type>は以下のいずれかである必要があります。
- **New**: 新しく作成
- **Add**: 要素を追加
- **Fix**: 内容を修正
- **Style**: 見た目のみの変更

#### Scope

スコープは、記事のマークダウンファイル名を短縮した物を使用します。
例えば、「Docker composeでNuxt.js 2.xの開発環境を整えるメモ」のスコープは、``nuxt-2-with-docker-compose``となります。

#### body

bodyには、変更内容を簡潔に記載してください。

### その他の変更

[AngularJS's commit message convention](https://zenn.dev/szn/articles/angularjs-commit-messeage-convention-jp)に準拠してください。
