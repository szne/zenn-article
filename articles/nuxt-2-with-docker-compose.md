---
title: "Docker composeでNuxt.js 2.xの開発環境を整えるメモ"
emoji: "⛰️"
type: "tech"
topics: ["docker", "nuxt"]
published: true
---

:::message
これはNuxt.js 2.x向けの文章です、Nuxt 3でこの方法が使用できるかどうかは不明です
:::
:::message
Docker、nuxt.jsを始めたての人が書いた文章です、
:::

## 更新履歴

:::details click

2022/3/5

- Nuxt 3(Beta)がリリースされているため、タイトルを変更しました、Nuxt 3向けの文章はしばらくしたら書くと思います。
- Zenn CLIを導入したついでに文章のslugを変更しました、これによりいいねがリセットされてしまいましたのでブクマ用にいいねしてた人はよければ再度お願いします。

2021/9/7

- 文章を公開しました
:::

個人用メモです、間違っていたら訂正お願いします。
いろいろなところに詰まった結果ほぼほぼこれと同じになりました
https://qiita.com/ngplus6655/items/6dd701450b9f9e8e2b86
手っ取り早く構築したい人はGitHubからどうぞ
https://github.com/szne/nuxt-with-docker

## 環境

```sh
> systeminfo
OS Name: Microsoft Windows 10 Home
OS Version: 10.0.19043 N/A Build 19043
```

```sh
> docker -v
Docker version 20.10.5, build 55c4c88
> docker-compose -v
docker-compose version 1.29.0, build 07737305
```

## Docker とかの下ごしらえ

プロジェクトを始めたいディレクトリに移動します、この文章では``NuxtProject``としています。

```diff
+ NuxtProject/
```

### Dockerfile の作成

> Docker は Dockerfile から命令を読み込み、自動的にイメージをビルドできます。
> [Dockerfile リファレンス](https://docs.docker.jp/engine/reference/builder.html)

DockerfileをNuxtProjectの中に追加します

```diff
NuxtProject/
+ └ Dockerfile
```

Dockerfile内は以下のように記述します

```Dockerfile:Dockerfile
FROM node:alpine

RUN apk update && \
    yarn global add create-nuxt-app

ENV HOST=0.0.0.0
EXPOSE 3000
```

### docker-compose.yml の作成

> Compose とは、複数のコンテナを定義し実行する Docker アプリケーションのためのツールです。
> [docker Compose 概要](https://docs.docker.jp/compose/overview.html)

docker-compose.ymlを追加します

```diff
NuxtProject/
+ ├ docker-compose.yml
  └ Dockerfile
```

```yml:docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    working_dir: "/src"
    volumes:
      - "./src:/src"
      - node_modules_volume:/src/node_modules
    tty: true
    environment:
    - CHOKIDAR_USEPOLLING=true
volumes:
  node_modules_volume:
```

書いてあることは[これ](https://qiita.com/ngplus6655/items/6dd701450b9f9e8e2b86#%E3%83%93%E3%83%AB%E3%83%89%E8%B5%B7%E5%8B%95)とほぼ同一です。ただし

- ホットリロードを有効化するために``CHOKIDAR_USEPOLLING=true``を追加
- マウントするボリュームの位置の変更
- node_modulesを

を行っています。
ちなみに、node_modulesのボリュームを指定しないと画面が表示されずに真っ白となります。

## コンテナの起動

Dockerイメージを起動します

```sh
> docker compose up -d
```

> ``-d``オプションでコンテナはバックグラウンドで起動し、そのまま実行し続けます。
> [Docker-docs-jaより](https://docs.docker.jp/compose/reference/up.html)
コンテナが起動していくかどうかを確認します

```sh
> docker-compose ps
NAME                SERVICE             STATUS              PORTS
nxts_app_1          app                 running             0.0.0.0:3000->3000/tcp
```

コンテナが起動しているのを確認したので次の工程に移ります
この時点でディレクトリはこうなっているはずです

```diff
NuxtProject/
├ src/
│ └ node_modules/
├ docker-compose.yml
└ Dockerfile
```

## Nuxt.js のインストール

### コンテナ内に接続

```sh
> docker-compose exec app /bin/sh
/src # 
```

### create-nuxt-app を使ってNuxt.js をインストール

```sh
/src # yarn create nuxt-app . --overwrite-dir
```

``--overwrite-dir``オプションはそこにあるファイルを上書きして生成するという手順です。つけないと``node_modules``があるのでつまります。
分からない場合は全部Enterを押せばうまくいくはずです、インストールには5分ほどかかります。
(もしもスクラッチで始めたい場合は[公式ドキュメント](https://ja.nuxtjs.org/docs/2.x/get-started/installation)に方法が乗っています）

## 起動

```sh
/src # yarn dev
yarn run v1.22.5
$ nuxt

   ╭────────────────────────────────────────╮
   │                                        │
   │   Nuxt @ v2.15.8                       │
   │                                        │
   │   ▸ Environment: development           │
   │   ▸ Rendering:   server-side           │
   │   ▸ Target:      server                │
   │                                        │
   │   Listening: http://172.23.0.2:3000/   │
   │                                        │
   ╰────────────────────────────────────────╯

ℹ Preparing project for development
ℹ Initial build may take a while
✔ Builder initialized 
✔ Nuxt files generated

✔ Client
  Compiled successfully in 15.11s

✔ Server
  Compiled successfully in 13.43s

ℹ Waiting for file changes
ℹ Memory usage: 164 MB (RSS: 253 MB)
ℹ Listening on: http://172.23.0.2:3000/

```

## 最終的なディレクトリ構成

```diff
NuxtProject/
├ src/
│ ├ .nuxt/
│ ├ components/
│ │ └ Tutorial.vue
│ ├ node_modules/
│ ├ pages/
│ │ └ index.vue
│ ├ stalic/
│ ├ store/
│ ├ .editrconfig
│ ├ nuxt.config.js
│ ├ package.json
│ └ yarn.lock
├ docker-compose.yml
└ Dockerfile
```

## トラブルシューティング

### ページが表示されない(ERR_EMPTY_RESPONSE)

package.jsonの``"scripts":{"dev": nuxt}``を以下のようにする

```json:package.json
"scripts": {
  "dev": "HOST=0.0.0.0 PORT=3000 nuxt",
  ︙
  ︙
}
```

## 参考

https://ja.nuxtjs.org/docs/2.x/get-started/installation
https://qiita.com/shun012526/items/a9137fcbaae303500ce1
https://qiita.com/ngplus6655/items/6dd701450b9f9e8e2b86
https://www.ikkitang1211.site/entry/2020/10/11/213824

## 使用したツール

https://codogue.com/asciitree/

---

### 最後に

途中でなぜかページ表示されずに3日くらい詰まってたんですけど[この記事](https://qiita.com/ngplus6655/items/6dd701450b9f9e8e2b86)で全部解決しました。
もともとはあまりいい感じの導入方法が書かれている記事がないなーっていう理由で書いたんですけど必要性ないですね。
上の記事を書いてくれた[ngplus6655](https://qiita.com/ngplus6655)さん、ジレンマから救っていただき本当にありがとうございました。
