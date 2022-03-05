---
title: "メモ: AngularJS's commit message convention 日本語訳"
emoji: "📝"
type: "idea"
topics: ["git"]
published: true
---

個人用です

翻訳元
https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines
英語ドキュメント
https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y/edit
Released under the MIT license
https://opensource.org/licenses/mit-license.php

## Git Commit Guidelines

私たちは、git commit メッセージのフォーマットについて非常に厳密なルールを設けています。 これにより、**より読みやすいメッセージ**になり、**プロジェクトの履歴**を見たときにもわかりやすくなります。 

コミットメッセージのフォーマットは、典型的なgitワークフローを使って追加することもできますし、CLIウィザード([Commitizen](https://github.com/commitizen/cz-cli))を使って追加することができます。
ウィザードを使うには，変更をステージングした後，ターミナルで `yarn run commit` をターミナルで実行してください。

### Commit Message Format
各コミットメッセージは、**header**、**body**、**footer**で構成されています。 ヘッダーは特別な形式で**type**、**scope**、**subject**を含む特別なフォーマットがあります。

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

**header**は必須で、ヘッダーの**scope**は任意です。

GitHubや、さまざまな git ツールでメッセージを読みやすくするために、コミットメッセージのどの行も100文字を超える入力はできません。

### Revert
前のコミットを取り消す場合は、``revert: ``で始まり、コロンの後に取り消されたコミットのヘッダーが続きます。
本文では、``This reverts commit <hash>.``と記述します。ここで、hashは取り消されたコミットのSHAです。

### Type
Typeは以下のいずれかである必要があります。

- **feat**: 新機能
- **fix**: バグの修正
- **docs**: ドキュメントのみの変更
- **style**: コードの動作に影響しない、見た目だけの変更（スペース、フォーマット、欠落の修正、セミコロンなど)
- **refactor**: バグの修正や機能の追加ではないコードの変更
- **perf**: パフォーマンスを向上させるコードの変更
- **test**: 不足しているテストの追加や既存のテストの修正
- **chore**: ビルドプロセスやドキュメント生成などの補助ツールやライブラリの変更

### Scope
スコープは、コミット変更の場所を指定するものであれば何でも構いません。
例えば
`$location`, `$browser`, `$compile`, `$rootScope`, `ngHref`, `ngClick`, `ngView`, 
などです。

変更が2つ以上の場所に影響を与える場合は、スコープに`*`を使用することができます。

### Subject
Subjectには、変更内容を簡潔に記載してください。

- 命令形の現在形を使用してください、例えば"change"なら"changed"や"changes"は使用できません
- 最初の文字を大文字にしないでください
- 末尾にドット (.) を付けないでください

### Body
**Subject**と同様に、命令形の現在形を使います。
Bodyでは、変化の動機を述べ、以前の行動と対比させます。

### Footer
すべての変更点は、フッターにBreaking Changeブロックとして記述する必要があります。
**Breaking Change**は、`BREAKING CHANGE:` という単語で始まり、スペースまたは2つの改行を入れます。コミットメッセージの残りの部分は、変更点の説明、正当性の証明、移行に関する注意点です。

詳しい説明はこちらの[ドキュメント](https://docs.google.com/document/d/1QrDFcIiPjSLDn3EL15IJygNPiHORgU1_OOAqWjiDU5Y)をご覧ください。