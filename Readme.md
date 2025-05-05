# zenn-article

## 必要なもの
- node.js
- GitHub cli

## 使い方

```fish
gh repo clone szne/zenn-article
npx zenn preview
```

## コミットメッセージ

### articles, books

フォーマット

```
<type>: <scope>
<subject>
```

#### Type

- **🎉New**: 新しく作成
- **✨Add**: 要素を追加
- **🚧Fix**: 内容を修正
- **✅Pub**: 記事を公開
- **☑️Arc**: 記事を非公開
- **❌Del**: 記事を削除
- **🔁Style**: 見た目のみの変更
- **📃Chore**: 補助ツールやライブラリの変更

#### Scope

スコープは、記事のマークダウンファイル名を短縮した物を使用します。
たとえば、「Docker composeでNuxt.js 2.xの開発環境を整えるメモ」のスコープは、``nuxt-2-with-docker-compose``となります。

#### subject

subjectには、変更内容を簡潔に記載します。
