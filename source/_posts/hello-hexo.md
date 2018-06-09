---
title: 技術メモはじめました
date: 2018-06-09 23:49:30
category: programming
tags: hexo
---
技術系ネタのメモ書き場所として、ブログっぽいものを作ってみました。
今までは某プログラマの技術情報共有サービスに書いていたのですが、
より気軽にポンポン書ける場所が欲しいなーと思い、一念発起してページを立ち上げてみました。

# このサイトの構築について
このサイトは、Node.js製の静的サイトジェネレータである[Hexo](https://hexo.io/)と
GitHubによる静的サイトホスティングサービス[GitHub Pages](https://pages.github.com/)を組み合わせて構築しています。
サイト公開までの手順はだいたいこんな感じ。

1. 新規hexoプロジェクト作成
2. hexoプロジェクト初期設定
3. Markdownで記事を書く
4. GitHub Pagesにデプロイ

サーバ構築不要で、使用するツールもGit、Node.js、お好みのテキストエディタ、と
かなり手軽にブログっぽいサイトを立ち上げることが可能です。
何番煎じか分かりませんが、構築手順をメモとして残します。

# サンプル(このサイトのリポジトリ)
[https://github.com/sashimi343/chira-ura](https://github.com/sashimi343/chira-ura)

# 新規hexoプロジェクト作成
## hexoインストール
**前提として、Node.jsとnpmが必要。**
```sh
# npm install -g hexo
```

## hexoプロジェクト作成
プロジェクトを作成するディレクトリ(Documents等)で以下のコマンドを実行する。

```sh
$ hexo init my-blog
$ cd my-blog
$ npm install
```

# hexoプロジェクト初期設定
## \_config.yml修正
hexoプロジェクトフォルダ(上記の例ではmy-blog)直下の\_config.ymlに
サイトに関する設定を記載する。
私が修正した箇所は下記のとおり。
参考: [Configuration | Hexo](https://hexo.io/docs/configuration.html)

* title: サイトのタイトル
* subtitle: サイトのサブタイトル
* description: サイトの説明文
* author: 作成者名
* language: サイトの言語設定。後述するテーマがこの値を使用する。`en`(英語)に設定
* timezone: タイムゾーンの設定。空欄でも良いらしいが念のため`Asia/Tokyo`に設定
* url: サイトのURL。GitHub Pagesにデプロイする場合、`https://GitHubユーザ名.github.io/リポジトリ名`
* root: コメントにも書いてあるとおり、サブディレクトリ名の設定が必要。今回はリポジトリ名
* permalink: 各記事のpermalinkのフォーマット。私は`:category/:title/`とした。使用可能なパラメータは[Permalinks | Hexo](https://hexo.io/docs/permalinks.html)あたりを参照
* public_dir: ビルドされた静的コンテンツが格納されるフォルダ。後述するGitHub Pagesとの兼ね合いで、デフォルトの`public`から`docs`に変更する

## テーマ変更
Hexoはカスタムテーマをダウンロードまたは作成することで、
ページの見た目を簡単にカスタマイズできる。
現在公開されているテーマは[このへん](https://hexo.io/themes/)から確認可能。
今回は[Archer](http://firework.studio/archer-demo/)を選択してみた。

## カスタムテーマのインストール
基本的な流れとしては下記の通り。

1. themesディレクトリ以下にテーマをclone
2. 必要に応じてthemes/<テーマ名>/\_config.yml等のファイルを編集
3. hexoプロジェクトフォルダ直下の\_config.ymlを編集し、適用するテーマを変更

```sh
$ git clone https://github.com/fi3ework/hexo-theme-archer themes/archer
$ cd themes/archer
$ npm i hexo-generator-json-content --save && npm i --save hexo-wordcount
```

npmコマンドでhexoのプラグインを2つインストールしているが、これはarcherテーマのREADME.mdに書かれていたもの。
なお、コマンドや修正内容までは載せないが、テーマの\_config.ymlとlayouts/post.ejsを編集している。

## .gitignore修正
.gitignoreファイルを確認し、`docs/`が記載されている場合は該当行を削除する。
(最終的にこのディレクトリの内容をGitHub Pagesで公開するため)

# Markdownで記事を書く
`$ hexo new hello-hexo`を実行すると、source/_posts/hello-hexo.mdが作成される。
あとはこのhello-hexo.mdを楽しく編集する。

`$ hexo server`コマンドを実行すると、`http://localhost:4000/ルート`でサーバが起動し、編集中の内容をブラウザで確認できる。
必要に応じて画面表示を確認しながら記事を書き進める。

一通り書き終えたら、`$ hexo generate`コマンドで静的ファイルを作成する。

# GitHub Pagesにデプロイ
GitHub上にリポジトリを新規作成し、作成したhexoプロジェクトをGitHubにpushする。
その後、GitHub上でリポジトリの設定を変更する。
リポジトリのSettings＞Options＞GitHub Pagesから、Sourceを`master branch /docs folder`に変更する。
こうすることで、このリポジトリのdocsフォルダ以下が静的サイトとして公開される。
(hexoプロジェクトの設定で静的ファイルの格納ディレクトリ名をdocsにしたのはこのため)

作成したサイトは`https://GitHubユーザ名.github.io/リポジトリ名/`にアクセスすることで閲覧できる。

# 終わりに
Hexoのデプロイコマンドを使わないなど、若干イレギュラー気味な手順ではありますが、
この手順でお手軽にブログっぽいサイトを立ち上げることができました。
Gitや使い慣れたテキストエディタで作業でき、執筆に集中できるのは大きいですね。

これからも、空き時間を使ってちまちま情報を増やしていければと思います。
