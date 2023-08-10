---
title: "CapsLockキーで英数にするとき全角英数にならないようにする"
emoji: "🅰️"
type: "idea"
topics: ["windows"]
published: true

---

## 更新履歴

:::details click

yyyy/mm/dd
- 記事を公開しました

:::

---

私は普段CapsLockキーで入力モードを切り替えています。
キーの位置も手ごろで、使用している上で支障はなかったのですが、最近になってかな入力から英数入力に切り替えたとき、全角英数で入力するようになってしまうことが頻発しました。
![全角英数字のマーク](https://storage.googleapis.com/zenn-user-upload/4d1d00078a87-20230810.png)
エディタとしてneovimを使うようになり、入力を切り替えた際に全角英数になってしまうことによる操作ミスが頻発するのでこれを修正します。

## 環境

```
エディション	Windows 11 Home
バージョン	22H2
OS ビルド	22621.2134
エクスペリエンス	Windows Feature Experience Pack 1000.22659.1000.0
```
使用しているIMEはWindows 10 May 2020 Update以降のMicrosoft IMEです。
もしもそれ以前のIMEを使用しているならば、[このサイト](https://faq.nec-lavie.jp/qasearch/1007/app/servlet/relatedqa?QID=021228)にそれっぽい解決方法が記載されています。

## そもそもなぜ起こるのか

Microsoft 日本語 IMEでは、CapsLock（英数）キーを投下することで、**日本語入力内で**ひらがなモードと英数字モードを切り替えを行っています。このモードでは、前回の変換結果にしたがって候補が表示される仕様となっており、その結果としてこのような事象が発生してしまいます。
ちなみに半角/全角（漢字）キーでは日本語入力自体のオンオフを行っているため、このようなことは発生しません。

## じゃあCapsLockを半角/全角として扱えばいいじゃん

ということを今から行っていきます。

### PoworToysのインストール

https://learn.microsoft.com/ja-jp/windows/powertoys/install
上記サイトよりインストールしてください。
個人的にはwingetを使用したインストールが好みなのでwingetを使用してインストールします。
```bash
winget install Microsoft.PowerToys --source winget
```

### PowerToysの設定を開く

インストールしたら、ステータスバーにPowerToysのアイコンがあると思うのでクリックします。
その後、下記のような画面が出たら、右下にある設定ボタンをクリックします。
![PowerToysのショートカット](/images/prevent_full-width_characters/image1.png)

### Keyboard managerからキーを再マップする

PowerToysの設定が開いたら、左側のメニューからKeyboard managerを選択し、
![Ketboard managerを開いた画面](/images/prevent_full-width_characters/image2.png)
キーの再マップからCapsLockを半角/全角キーに割り当てます。

以上の操作を行うことで、CapsLockキーで英数にするとき全角英数にならないようにすることができます。
お疲れ様でした。

## 参考にしたもの

https://e-words.jp/w/%E5%8D%8A%E8%A7%92-%E5%85%A8%E8%A7%92%E3%82%AD%E3%83%BC.html
https://faq.nec-lavie.jp/qasearch/1007/app/servlet/relatedqa?QID=021228
https://atmarkit.itmedia.co.jp/ait/articles/2202/17/news021.html
