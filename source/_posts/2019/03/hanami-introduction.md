---
title: "[Ruby] Webアプリケーションフレームワーク Hanamiを触ってみた"
date: 2019-03-03 15:00:05
category: programming
tags:
  - ruby
  - hanami
  - ruby-on-rails
---
RubyのWebアプリケーションフレームワークと言えばRuby on Rails以外に有力な候補がいないイメージでしたが、
Hanamiというフレームワークが結構頑張っており、かつ自分好みなアーキテクチャだったので、ここで紹介する。

# Hanamiとは
[Hanami](https://hanamirb.org/)はRuby製の比較的新しいWebアプリケーションフレームワークだ。
Hanamiの特徴としては、RailsのようなMVCアーキテクチャではなく、ドメイン駆動設計(Domain Driven Design; DDD)を採用している点である[^1]。
アプリケーションのコア(ビジネスロジック)とUIに関する部分が分離されており、
プロダクトが大規模になってもメンテナンス性の高い設計・コードを維持しやすい。

私がHanamiに興味を持ったきっかけは、まさにこのメンテナンス性の高さにある。
Railsは小規模なプロダクトを素早く作成するのには最適だが、改修を繰り返して長期間運用し続けると、どうしてもメンテナンスしづらくなりがちである。
肥大化したモデルをなんとかしようと、[自作のサービス層を導入](https://sashimi343.github.io/chira-ura/2018/06/10/rails-service-layer/)してDDDもどきを採用してみたりもしたが、根本解決には至らなかった。
そんな中、フレームワークの設計思想がDDD寄りになっており、素早い開発よりも高いメンテナンス性を重視したHanamiというフレームワークに出会った。

# Hanamiのインストール
`gem install hanami`でOK。
執筆時点ではv1.3.1までリリースされている。

# プロジェクトの構成
新規Hanamiプロジェクトは、Railsのように`hanami new <appname>`コマンドで作成できる。
プロジェクトのディレクトリ構成は下記のようになっている。
ビジネスロジックに関するコードはlib/以下に、UIに関するコードはapps/以下に格納される。

```
 |--Gemfile
 |--Rakefile
 |--apps/
 |  |--web/
 |  |  |--application.rb
 |  |  |--assets/
 |  |  |--config/
 |  |  |  |--routes.rb
 |  |  |--controllers/
 |  |  |--templates/
 |  |  |--views/
 |--config/
 |--config.ru
 |--db/
 |  |--migrations/
 |  |--schema.sql
 |--lib/
 |  |--hanami_introduction/
 |  |  |--entities/
 |  |  |--interactors/
 |  |  |--mailers/
 |  |  |--repositories/
 |--public/
 |--spec/
 |  |--web/
 |  |  |--controllers/
 |  |  |--views/
 |  |  |--features/
 |  |--hanami_introduction/
 |  |  |--entities/
 |  |  |--interactors/
 |  |  |--mailers/
 |  |  |--repositories/
```

説明の都合上、`hanami new`コマンドでは作成されない2つのディレクトリ(lib/hanami_introduction/interactors, spec/hanami_introduction/interactors)を手動で追加している。

以下、各層の役割について簡単に説明する。

# Hanamiの各層の役割

## UI(アプリケーション層)
UI(アプリケーション層)はRouter, Controller, View, Templateなどから構成される。

### Router

### Controller

### View, Template

## ビジネスロジック(ドメイン層)
ビジネスロジック(ドメイン層)はEntity, Repository, Interactorから構成される。

### Entity
Entityはその名の通り、ビジネスロジック上の実体(Entity)を表す層で、lib/<appname>/entities以下に配置される。
EntityクラスはRDBMSのテーブルに対応し、そのインスタンスはRDBMSのレコードに対応する。
Entityの役割は、ビジネスロジック上の実体とそれに付随する操作を表現することである。

なお、EntityそのものにはRDBMSに対するSQLの発行機能はなく、RDBMSへのアクセスはRepository層が行う。
Rails(Active Record)ではモデルに対する操作(ビジネスロジック)とデータの永続化ロジックが同じクラスに記述されていたが、Hanamiではそれらが分離されており、モデルが肥大化しにくくなっている。

また、HanamiのEntityの特徴としてイミュータブルである(コンストラクタ以外でメンバの値を変更できない)ことが挙げられる。
この仕様については賛否両論があるかもしれないが、今は言及しない。

### Repository

### Interactor

# 参考
* [Hanami](https://hanamirb.org/)
* [Hanami Guides](https://guides.hanamirb.org/)
* [HanamiはRubyの救世主(メシア)となるか、愚かな星と散るのか](https://magazine.rubyist.net/articles/0056/0056-hanami.html)

[^1]: 正確には、HanamiはDDDではなくビヘイビア駆動開発(Behavior Driven Development; BDD)と呼んでいる
