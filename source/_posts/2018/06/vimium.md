---
title: Webブラウザ上でVimコマンドを使えるようにするChrome拡張「Vimium」が便利
date: 2018-06-24 20:47:49
category: technology
tags:
  - chrome
  - vim
---
普段からVimを使っていると、何でもかんでもVimのキーバインドで操作したくなるのが人間というもの。
IDE(Eclipse, Visual Studio等)にVimのキーバインドを取り入れるためのプラグインは比較的有名だが、
Webブラウザ(Google Chrome)にもVim風操作を実現するための拡張機能「Vimium」がある。
これが割と快適なので、宣伝がてら導入・設定についてメモ。

# 導入方法
Chromeウェブストアで「Vimium」を検索してインストールするだけ。

[Vimium - Chrome ウェブストア](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=ja)

見慣れた「V」のアイコンがChromeに追加されたらインストールは完了。
特にこだわりがなければこのまま使っても良いが、多少カスタマイズしたほうが使いやすい。
設定画面にはVアイコンをクリック→「Options」から入れる。

# 個人的Vimiumカスタマイズ例
## Excluded URLs and keys
Vimキーバインドを使わないページのURLを設定できる。
ページにもともとショートカットキーが設定されており、Vimのキーバインドと衝突してしまう場合にはVimiumの対象外サイトに指定する必要がある。
私は下記3URLを設定している。

* `https?://mail.google.com/*`
* `https?://calendar.google.com/*`
* `https?://tweetdeck.twitter.com/*`

## Custom key mappings
デフォルトのキーマッピングを上書きする設定。
マップ可能なコマンドは「Show available commands」から確認可能。
以下に私の設定を記載する。
デフォルトの設定(やVim本来の操作)から極力逸脱しないようにしている。

```txt
# Insert your preferred key mappings here.
map h goBack
map l goForward
map H previousTab
map L nextTab
map i LinkHints.activateMode
map I LinkHints.activateModeToOpenInNewTab
map f scrollFullPageDown
map b scrollFullPageUp
```

これ以降はAdvanced Optionsのため、「Show Advanced Options」をクリックしないと表示されません。

## Scroll step size
j/k等でスクロールする際の移動分。
デフォルトだとスクロール幅が小さかったため、80pxと少しだけ大きくしてみた。

## Characters used for link hints
i/I等を押すと、画面内のリンクを選択するモードになるのだが、その際に各リンクに割り当てられるヒント文字の一覧を定義する。
Vimのプラグイン[vim-easymotion](https://github.com/easymotion/vim-easymotion)を思い浮かべるとだいたいあってる。
文字を増やせば1キーで選択できるリンクが多くなるが、その分打ちづらいキーを押さなければならなくなる。
タイプ数の少なさを優先するか、打ちやすさを優先するかは好みで。

私の設定

```txt
hjklasdfgyuiopqwertnmzxcvb
```

vim-easymotionと違って、書いた順番で文字の優先順位が決まるわけではなさそう？

# Vimiumを使ってみる
Vim使いの方であれば特に違和感なく(Vim感覚で)Webブラウザを操作できる。
また、i(私の設定では。デフォルトはf)を押すと、リンクを選択するモードになる。

<img src="/chira-ura/assets/images/vimium.png" width="80%" alt="Vimium使用例" />

例えば、この状態で「q」とタイプするとChromeウェブストアに移動できる。

# より詳細な使い方
このへんを読むと幸せになれるかもしれない。

[Home · philc/vimium Wiki](https://github.com/philc/vimium/wiki)
