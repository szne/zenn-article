---
title: "Docker composeでNuxt 3の開発環境を作成する"
emoji: "⛰️"
type: "tech"
topics: ["docker", "docker-compose", "nuxt", "nuxt 3"]
published: true
---

## 更新履歴

:::details click

2022/3/11

- 記事を公開しました
:::

個人用メモです、間違っていたら訂正お願いします。
https://zenn.dev/szn/articles/nuxt-2-with-docker-compose
これのNuxt 3バージョンのようなものです。

## 環境

```sh
> sw_vers
ProductName: macOS
ProductVersion: 12.2.1
BuildVersion: 21D62
```

```sh
> docker -v
Docker version 20.10.12, build e91ed5707e
> docker-compose -v
Docker Compose version 2.3.0
```

これを見ればわかると思いますが、前回から実行環境が大きく変わり、WindowsからMac OSへとなっております。
それに伴い、動作確認もMac OSのみとなります、もしWindowsマシンで作動しなかった場合はご連絡くれると嬉しいです。

## Docker とかの下ごしらえ

プロジェクトを始めたいディレクトリに移動します、この文章では``NuxtProject``としています。

```diff
+ Nuxt3Project/
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
FROM node:lts-alpine

RUN mkdir src

RUN apk update && \
    apk upgrade && \
    apk add git && \
    npm install -g npm@latest nuxi nuxt3
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
    volumes:
      - ./src:/src:cached
      # - node_modules:/src/node_modules
      # ↑ 次回起動後にコメントアウトを解除
    working_dir: "/src"
    ports:
      - "3000:3000"
    tty: true
    environment:
    - HOST=0.0.0.0
    - port=3000
    - CHOKIDAR_USEPOLLING=true

volumes:
  node_modules:
```

node_modulesをボリュームに指定する部分がコメントアウトになっています、この理由は後ほど説明します。

## コンテナを起動（1回目）

Dockerイメージを起動します

```sh
> cd NuxtProject
> docker compose up -d
```

> ``-d``オプションでコンテナはバックグラウンドで起動し、そのまま実行し続けます。
> [Docker-docs-jaより](https://docs.docker.jp/compose/reference/up.html)

初回の起動には1分半ほど時間がかかります。
コンテナが起動しているかどうかを確認します（任意）

```sh
> docker compose ps
NAME                       COMMAND                  SERVICE             STATUS              PORTS
NuxtProject-app-1          "docker-entrypoint.s…"   app                 running             0.0.0.0:3000->3000/tcp
```

コンテナが起動しているのを確認したら次の工程へと移ります
この時点でディレクトリはこうなっているはずです

```diff
NuxtProject/
+ ├ src/
  ├ docker-compose.yml
  └ Dockerfile
```

## Nuxt.js のインストール

### コンテナ内に接続

```sh
> docker-compose exec app sh
/src # 
```

### npx nuxi を使ってnuxt/starterをインストール

```sh
/src # npx nuxi init .
Nuxt CLI v3.0.0-27447229.20f3171 
ℹ cloned nuxt/starter#v3 to /src
 🎉  Another geometric Nuxt project just made! Next steps:

     📁  `cd `

     💿  Install dependencies with `npm install` or `yarn install`

     🚀  Start development server with `npm run dev` or `yarn dev`
```

この時点でディレクトリはこうなっているはずです

```diff
Nuxtproject/
  ├ src/
+ │ ├ .gitignore
+ │ ├ app.vue
+ │ ├ nuxt.config.ts
+ │ ├ package.json
+ │ ├ README.md
+ │ ├ tsconfig.json
+ │ └ yarn.lock
  ├ docker-compose.yml
  └ Dockerfile
```

#### （余談）docker-composeのコメントアウトの理由

nuxt 3をインストールする際に使用する``nuxi``は作成するディレクトリにファイルが存在するとうまく作動しません、またnuxt 2.xまででの``create nuxt-app``の``--overwrite-dir``オプションを使用することができません。
Dockerでボリュームを設定すると（short記法の場合）ディレクトリにファイルが生成され、``nuxi``がうまく作動しなくなるため一時的にコメントアウトしています。

## node_modulesにボリュームを割り当てる

今現在、コンテナ内ではnode_modulesを含むすべてのディレクトリをbindしている状態ですが、このままinstallすると時間がかなりかかってしまうため、node_modulesにボリュームを割り当てます

### docker-compose.ymlの書き換え

``- node_modules:/src/node_modules``のコメントアウトを外します。

```yml:docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    volumes:
      - ./src:/src:cached
      - node_modules:/src/node_modules
      # ↑ コメントアウトを解除
    working_dir: "/src"
    ports:
      - "3000:3000"
    tty: true
    environment:
    - HOST=0.0.0.0
    - port=3000
    - CHOKIDAR_USEPOLLING=true

volumes:
  node_modules:
```

### Dockerの再起動

```sh
/src # exit
> docker compose down 
> docker compose up -d
```

これで``/src``の中にnode_modulesという空のディレクトリが表示されたと思います。
この中をいじりたい場合は下記の記事を参考にするといいかもしれません。
https://zenn.dev/foolishell/articles/3d327557af3554

## nuxt 3のインストール

### 再度コンテナ内に接続

```sh
> docker-compose exec app sh
/src # 
```

### nuxt.jsのインストール

```sh
/src # yarn install
yarn install v1.22.17
Nuxt CLI v3.0.0-27447229.20f3171

  > Local:    http://localhost:3000/ 
  > Network:  http://172.x.x.x:3000/

ℹ Vite warmed up in xxxxms
ℹ Vite warmed up in xxxxms 
✔ Vite server built in xxxxms 
✔ Nitro built in xxx ms             
```

<http://localhost:3000/>にアクセスし、以下の画面が出たらインストール完了です

![Nuxt.js初期画面](/images/nuxt-3-with-docker/success.png)

お疲れ様でした。

:::details 最終的なディレクトリ構成

```diff
Nuxtproject/
  ├ src/
+ │ ├ .nuxt/
+ │ ├ node_modules/
  │ ├ .gitignore
  │ ├ app.vue
  │ ├ nuxt.config.ts
  │ ├ package.json
  │ ├ README.md
  │ ├ tsconfig.json
  │ └ yarn.lock
  ├ docker-compose.yml
  └ Dockerfile
```

:::

## 終了〜次回以降の起動

### 終了方法

```sh
^c
/src # exit
> docker-compose stop
```

### 次回以降の起動方法

#### 起動

```sh
> cd NuxtProject
> docker-compose up -d
> docker-compose exec app sh
/src # yarn dev -o
```

## 参考

https://v3.nuxtjs.org/getting-started/installation

https://zenn.dev/paryugh/articles/7559e255fbfc2e

https://qiita.com/hyamatan/items/0d8c0b318f71aa130fce

https://hi97.hamazo.tv/e8859609.html

https://qiita.com/ngplus6655/items/6dd701450b9f9e8e2b86
