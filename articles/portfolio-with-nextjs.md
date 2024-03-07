---
title: "無知だけどNext.jsでポートフォリオサイトを作ったから見て"
emoji: "🎉"
type: "idea"
topics: ["nextjs", "react"]
published: false
---

## 更新履歴

:::details click

2024/3/8
- 記事を公開しました

:::

next.jsと仲良くなるためにポートフォリオサイトを作ってみました。
zennで「next.js　ポートフォリオ」と検索したら13件の記事が見つかったので14番煎じとなりますが、折角作ったので紹介させてください。

作ったサイトはこちら: 

https://szn.jp/

ソースコードはこちら: 
https://github.com/szne/szn.jp

## 主に使用したもの

- Next.js(App Router)
  - 最近できた機能だからかあまり情報がなくて大変でした
  - 英語勉強しないとな
- @funtech-inc/mekuri, gsap
  - ページ遷移アニメーションのために使用
  - 作成した時点ではFramer MotionはApp Routerに対応していなかったので大変助かりました
- react-markdown, gray-matter, rehype-raw, remark-gfm
  - markdownをページにするために使用
- Vercel
  - 無料でサイトをデプロイできるすごいやつ

## 頑張ったところ

### トップページのモーションロゴ

アクセスした際にモーションロゴを表示させたいという気持ちがあり、それを前提にロゴを設計しました。

SVGをtsxに埋め込むのが多少大変でしたが、埋め込んだ後は``@keyframes``を使用して制御するだけのお手軽実装です。

```scss:page.module.scss
  svg {
    //...
    stroke: var(--main-color);
    stroke-width: 30px;
    stroke-dasharray: 5920;
    -webkit-animation: hello 5s cubic-bezier(0.31, 0.005, 0, 1);
    animation: hello 5s cubic-bezier(0.31, 0.005, 0, 1);

    @keyframes hello {
      0% {
        stroke-dashoffset: 5920;
      }
      100% {
        stroke-dashoffset: 0;
      }
    }
  }
```

モーションロゴが表示された後、スクロールダウンバーが表示されるようにするため、スクロールダウンバーのアニメーションは2つ設定されています（1つは遅延用で、2つ目が無限ループされる）。

```scss:page.module.scss
.scroll_down {
    //...
    animation:
      scroll_delay 5s,
      scrollDown 2s cubic-bezier(0.6, 0, 0, 0.6) 5s infinite;
    
    @keyframes scroll_delay {
        //何もしない
    }
    @keyframes scrollDown {
        //...
    }
  }
```

#### 参考にしたサイト

https://www.torat.jp/svg_animation/

### ダークモード対応

最近のモダンなwebサイトはどこもダークモードに対応しているので実装してみました。

![ライトモード時のトップページ](/images/portfolio-with-nextjs/top.png)
*ライトモード時のトップページ*

![ダークモード時のトップページ](/images/portfolio-with-nextjs/dark.png)
*ライトモード時のトップページ*

使用する色をあらかじめ変数として``root.scss``に記述しておき、カラースキームによって変数に代入する色を変更することで少ない記述で実装できるようにしています。

ロゴもsvgを埋め込む形式だったので、対応は非常に簡単でした。

ビバ！変数

```scss:root.scss
:root {
  --bg-color: #f5f5f5;
  --text-color: #122234;
  --link-color: #3168aa;
  --main-color: #79bde9;
  --shadow-color: #d0d0d0;
}

@media (prefers-color-scheme: dark) {
  :root {
    --bg-color: #122234;
    --text-color: #f5f5f5;
    --link-color: #e7c299;
    --main-color: #f3aa56;
    --shadow-color: #0f1d2c;
  }
}
```

```scss:globals.scss
body {
  background-color: var(--bg-color);
  color: var(--text-color);
  font-family: var(--body-font);
}
```

### 404ページ

アクセスするたびにランダムな文言が出るようにしました。小さい実装だけどお気に入りです。

## 詰まったとこ

### ページ遷移アニメーションの実装

先述したように、Framer MotionはどうやらApp Routerに対応していないようなので@funtech-inc/mekuriというライブラリを使用しました。

https://github.com/FunTechInc/mekuri

初学者ゆえ今までは検索エンジンを使用して使用方法を得ていましたが、あまりにも情報が少なったため難儀しました。
実装するためにはGitHubにあるソースコードを読解するしかなく、実装したことで自身の検索能力が向上した気がします。

### Vercel環境でのパスエラー

サイト制作がひと段落して、Vercelを使用してデプロイしようとしたところ、動的生成されるページにあった画像部分のパスがエラーとなってしまいました。
画像のパスを``/public/hogehoge/..``と指定していたのが原因だったらしく（動作しているサーバーのルートディレクトリを指してしまうから？）、``<Image>``を使用するのをやめて``<img>``を使用するとともにパスを相対パスに変更することで解決しました。

このエラーで1か月ほど詰まっていたので解決できて本当に良かったです。
https://bsky.app/profile/szn.jp/post/3kn37mjs6vs2l

## 今後実装したいこと

- スムーズなスクロール
- カスタムスクロールバー

## え、、これnext.js使う必要ある？

作ってる最中からこんな大きなフレームワーク使わなくてもいいのでは、という疑問が生じていました。

next.jsはおそらくとても便利な機能が詰め込まれていたり、うまく活用できたらものすごいパワーを発生させられるフレームワークなのだと思います。
しかし、自分の力量ではnext.jsの諸機能を能動的には（多分）作品ページの生成のみにしか使用できませんした。

とはいえ生のReactを書くよりはずいぶん楽だったのかな？

何もかもが謎なのでこれからの開発や勉強を通してこの質問への回答を見つけていきたいです。
ついでに有識者の人は所見をコメントしてくれたらうれしいです。

ありがとうございました。
