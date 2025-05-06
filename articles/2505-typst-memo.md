---
title: "Typstで使える日本語環境を構築する備忘録"
emoji: "📝"
type: "tech"
topics: ["typst"]
published: true
---

LaTeXのエラーに嫌気がさしたのでTypstへ乗り換えました。生成物の見た目とか調整するのにちょい詰まったのでやり方を備忘録として残しておきます。

Typstの導入は終わってて日本語環境の構築のみ興味ある人は[Typstの日本語環境の構築](#typstの日本語環境の構築)まで飛ばしてください。

### 使用環境

```sh
OS: Windows 11 Home Insider Preview (Build 26200)
Winget: v1.11.220-preview
PowerShell: 7.5.1
```

### 完成イメージ

![完成形のプレビュー](/images/2505-typst-memo/after.png)
*完成形のプレビュー*

#### ソース

:::details 📝ソース（長いので折り畳み）

```md:test.typ
#import "@preview/js:0.1.3": *
#show: js.with()

#show "、": "，" // 読点をカンマに置き換える
#show "。": "．" // 句点をピリオドに置き換える
// 句読点の一括変換もこの通り。

#maketitle(
  title: "Typstって何ができるの？",
  authors: "szne",
  abstract: [
    ここには説明が書けます。自分はまだ使ったことはないけど便利ですね。
  ],
  // コメントはこんな感じ、全体的にmdライクで親和性がよい
)

これのすごいところは *VSCode でリアルタイムで編集できるところ* @Typst。_zenn_の記事に`gif`埋め込もうとおもったけど手間がすごいので自分で体感してください。

= これは見出し1
== これは見出し2
=== これは見出し3

- 箇条書きリスト。もういちいち `\begin{itemize}` なんて書く必要はありません。
+ 番号付きリスト。これも `+` を入力するだけ
/ 用語リスト: 自分はあまり使わないけど便利そう

数式の書き方はちょっと違うから慣れるのは大変かも、けど慣れたらかなり早く書けそう

これはインライン数式 $e = m c^2, x = (b^2 - 4a c) / (2a)$ これはインラインじゃない数式 ↓

$
  x(t) = sum^oo_(n=-oo) X(f) e^(j 2 pi f t) d f
$

#grid(
  columns: 2,
  figure(
    image("icon.png", width: 40%),
    caption: "画像挿入も対応。もちろん表も",
  ),
  figure(
    caption: "これは表の例。関係ないけど横並びもできるよ",
    table(
      columns: (1fr, auto, auto),
      table.header(
        [*Date*],
        [Price],
        [Open],
      ),
      [May 05, 2025], [`3,259.56`], [`3,248.16`],
      [May 04, 2025], [`3,248.12`], [`3,239.67`],
    ),
  ),
)


ちなみになぜか $LaTeX$ も入力できる。これ以外にも色々できます。詳細は https://typst.app/docs/ を参照

#bibliography(
  "test.yml",
  style: "ieee",
) // 引用は .bib と The Hayagriva YAML File Format(.yml) の二種類が使用できるらしい。.ymlの記述がかなり楽なので後者をお勧め。詳細は https://github.com/typst/hayagriva/blob/main/docs/file-format.md を参照。

```

```yaml:test.yml
Typst:
    type: Web
    title: Typstで環境構築する備忘録
    author: szne
    date: 2025-05-05
    url: 
        value: https://zenn.dev/szn/articles/2505-typst-memo,
        date: 2025-05-05
```

:::

## TypstとVS Codeの導入方法

### 必要なもの

- **ターミナル**（自分は ``PowerShell 7.5.1`` を使用）
- [**Winget**](https://learn.microsoft.com/ja-jp/windows/package-manager/winget/)（CLIで使えるパッケージマネージャー） 

### Typstのインストール

```sh
winget install --id Typst.Typst -e
typst -V
```

``typst 0.13.1`` のようにバージョン情報が表示されれば成功です。

### VS Codeの導入

```sh
winget install --id Microsoft.VisualStudioCode --silent --override "/VERYSILENT /NORESTART /MERGETASKS=!runcode,addcontextmenufiles,addcontextmenufolders,associatewithfiles,addtopath"
```

#### 拡張機能の導入

VS Codeに以下の拡張機能を導入します。プロファイルを作って分けると便利かも。

- [**Tinymist Typst**](https://marketplace.visualstudio.com/items/?itemName=myriad-dreamin.tinymist): ほぼ必須、LaTeXで言う ``LaTeX WorkShop`` 的存在。ファイルを入力したらリアルタイムでプレビューを行ってくれます。
- [**vscode-pdf**](https://marketplace.visualstudio.com/items/?itemName=tomoki1207.pdf): 出力された ``.pdf`` を閲覧するために使ったりする。

もし必要なら以下の拡張機能も入れると便利かもしれません。

- [Japanese Language Pack for Visual Studio Code](https://marketplace.visualstudio.com/items/?itemName=MS-CEINTL.vscode-language-pack-ja): VS Codeの日本語化パック、たまにデフォルトで入ってる。

これらの拡張機能の導入後、VS Codeで ``.typ`` ファイルを開いたあと、**右上の虫眼鏡付きの本アイコンを押す**か ``Ctrl-k → v (v を押している時に Ctrl は押さない)`` でプレビューが開けます。リアルタイムで編集が反映されていく驚きを是非実感しましょう。

![プレビューアイコンの位置](/images/2505-typst-memo/open.png)
*プレビューアイコンの位置*

なお、**実際にPDFを出力するにはその左側のファイルアイコンを選択するか、CLIで``typst compile [[filename]].typ``を実行してください**。

## Typstの日本語環境の構築

ここまでの操作で一応Typstの環境を構築することはできますが、[完成形のコード](#ソース) を開いてみるとエラーが表示されるはずです。エラーの部分をすべてコメントアウトしても出力される画像は普段のレポートとはかけ離れたものとなっています。

![違和感ある出力結果](/images/2505-typst-memo/before.png)
*違和感がすごい出力結果*

### Typstテンプレートの導入

これを解決するために **Typstテンプレートを導入** します。多分LaTeXで言う ``\usepackage`` に近しいものです。有志が作成したさまざまなテンプレートを導入することで、本記事の目的である日本語環境の構築や、高度な図の描画^[[cetz](https://typst.app/universe/package/cetz/)]、スライドの作成^[[touying](https://typst.app/universe/package/touying/)]などができるようになります。

今回は三重大学の奥村晴彦名誉教授が作成した[**js**](https://typst.app/universe/package/js)（以下**typst-js**）を導入します。他にも木村駿介氏が作成した[jaconf-mscs](https://typst.app/universe/package/jaconf-mscs)というテンプレートなど、日本語環境を構築するテンプレートはいくつか存在します。将来的には自分に最適なテンプレートを探し出して使用するといいでしょう。

ターミナルにて以下のコマンドを実行します（ファイルが生成されるので汚れてもいいディレクトリで実行するのがオススメです）。

```sh
typst init @preview/js:0.1.3
```

実行後、カレントディレクトリに ``js`` というフォルダーが生成されるので、その中に入り ``example.typ`` がうまくコンパイルできるかを確認しましょう。

うまくコンパイルが確認出来れば日本語対応完了です。基本グローバルに導入されるので文章ごとに導入する必要はありません。

#### typst-jsの使用方法

ただ導入しただけでtypst-jsが自動的に適用されるわけではなく、文章を日本語対応にするためには ``.typ`` ファイルの冒頭に 

```md
#import "@preview/js:0.1.3": *
#show: js.with()
```

と記述する必要があります。それ以外の使用方法は生成された ``example.typ`` や [完成形のコード](#ソース) を参照してください。

### 原ノ味フォントの導入

しかしこのままではOSによってフォントがばらけるほか、やはり生成物に多少の違和感が生じるという可能性があります。ということで、TeX Live 2020以降に標準でPDFファイルに埋め込まれる日本語フォント^[[TEX Wiki](https://texwiki.texjp.org/?%E5%8E%9F%E3%83%8E%E5%91%B3%E3%83%95%E3%82%A9%E3%83%B3%E3%83%88)]である原ノ味フォントを導入します。

[原ノ味フォントのGitHubページ](https://github.com/trueroad/HaranoAjiFonts)にアクセスして、ZIPファイルをダウンロード。解凍してインストールします。

その後もう一度コンパイル（VS Codeなら再起動）すれば変更が適用されるはずですが、もし適用されなければ以下のコードを ``.typ`` ファイルに追記すればうまくいくはずです。

```diff md
#import "@preview/js:0.1.3": *

+ #show: js.with(
+   lang: "ja",
+   seriffont-cjk: "Harano Aji Mincho"
+   sansfont-cjk: "Harano Aji Gothic"
+ )
```

これで導入は完了です。お疲れ様でした。

## 参考文献

https://typst.app/docs
https://typst-jp.github.io/docs/
https://okumuralab.org/~okumura/misc/241111.html
https://github.com/okumuralab/typst-js
https://zenn.dev/nuits_jp/articles/winget-install-option
https://zenn.dev/kimushun1101/articles/typst-template
