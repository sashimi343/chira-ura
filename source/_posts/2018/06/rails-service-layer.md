---
title: "[Rails] コンパクトにサービス層を自作・導入する(ジェネレータ付き)"
date: 2018-06-10 16:43:55
category: programming
tags:
  - ruby
  - ruby-on-rails
---
# TL;DR
* Railsアプリにサービス層を導入したいんだけど、Trailblazerとか使うほどでもないんだよなぁ、という人（主に自分）向け
* 素のRailsアプリに自作サービス層を導入してみた
* ついでに`rails generate service hoge`みたいなジェネレータも作成してみた

# 経緯
既存のRailsアプリ（APIサーバ）にサービス層を導入しよう！と思い立ったものの、
Trailblazer等のgemを導入すると大掛かりな改修が必要になるため、少々敷居が高いと感じました。
そこで、既存プロジェクトにgemを追加せず、最低限の機能を持ったコンパクトなサービス層を自作して導入しようと考えました。

# 要件（サービス層に求めていたこと）
* **既存のRailsプロジェクトにgemを追加せずに導入できること**
* `rails generate service sample`のように、ジェネレータを用いてファイルを作成できること
* 1機能 1クラスのコンパクトなサービスクラス
* パラメータのバリデーションを行い、異常があれば例外を投げる（既存コードと同様のエラー処理を行うため）


# 方針
* `ActiveModel::Model`をincludeしたクラス（フォームオブジェクトのようなもの）を作成する
* 処理実行前のバリデーション実行を強制し、パラメータが正常な場合のみ処理を行うようにする
* バリデーションエラー時には`ActiveRecord::RecordInvalid`例外を投げる
* `rails generate generator`を使用し、ジェネレータを生成する
* パラメータはコンストラクタ経由で渡す（個人的な好みです）

# ソースコード
## app/services/base_service.rb
すべてのサービスクラスの親クラスです。
ポイントは、

* `ActiveModel::Model`をincludeし、パラメータのバリデーション機能を使用可能にする
* 処理実行前に必ずバリデーションを経由させる

の2点です。

```ruby
class BaseService
  include ActiveModel::Model

  # サービスを利用するクラスが呼び出すメソッド
  def provide()
    raise_validation_error if invalid?
    perform
  end

  private

  # サービスの処理（パラメータが正常な場合のみ実行されます）
  # 各サービスクラスではこのメソッドをoverrideして機能を実装します
  def perform()
    raise NotImplementedError.new("You must implement #{self.class}##{__method__}")
  end

  # バリデーションエラー時、ActiveRecord::RecordInvalidを投げるためのメソッド
  def raise_validation_error()
    raise ActiveRecord::RecordInvalid.new(self)
  end
end
```

## app/services/sample_service.rb
個々のサービスクラスは、上記の`BaseService`クラスを継承して作成します。
パラメータはコンストラクタ内でインスタンス変数にセットし、処理本体は`perform()`メソッド内に実装します。
また、必要に応じてバリデーションのためのコードを記述します。

（コードの雛形は後述するジェネレータで自動生成します）

```ruby
class SampleService < BaseService
  # パラメータ一覧
  attr_accessor :username, :password

  # バリデーション
  validates :username, presence: true
  validates :password, presence: true

  # パラメータはコンストラクタで渡す
  def initialize(username, password)
    @username = username
    @password = password
  end

  private

  # 処理本体（パラメータが正常な場合のみ実行されます）
  def perform()
    # ここに処理を記述します
  end
end
```

## ジェネレータ
ジェネレータは下記のコマンドで追加できます。
```sh
$ rails generate generator service
```
コマンド実行後、`lib/generators/service`が作成されるので、ジェネレータのコードを編集します。
ジェネレータクラスにあるすべてのインスタンスメソッドが（書いた順番に）実行されます。

```ruby
class ServiceGenerator < Rails::Generators::NamedBase
  source_root File.expand_path('../templates', __FILE__)

  # パラメータを定義
  argument :name

  # サービスクラスを作成する
  def create_service_file
    destination = Rails.root.join("app/services/#{name.underscore}_service.rb")
    template('service.rb.erb', destination)
  end

  # サービスクラスのテストクラスを作成する
  def create_test_file
    destination = Rails.root.join("test/services/#{name.underscore}_service_test.rb")
    template('test.rb.erb', destination)
  end
end
```

また、サービスクラスとそのテストクラスのテンプレートは下記の通りです。

### サービスクラスのテンプレート
```ruby
class <%= name.camelize %>Service < BaseService
  def initialize()
    # Set parameters...
  end

  private

  def perform()
    # Run this method if parameters are valid
  end
end
```

### テストクラスのテンプレート
```ruby
require 'test_helper'

class <%= name.camelize %>ServiceTest < ActiveSupport::TestCase
  # test "the truth" do
  #   assert true
  # end
end
```

# 使い方
## サービスクラス作成
下記のコマンドを実行します

```sh
$ rails generate service sample
```

実行後、`app/services/sample_service.rb`と`test/services/sample_service_test.rb`が作成されますので、
適宜修正してください。

## controllerからの呼び出し
前述のように、パラメータはコンストラクタの引数として渡します。
また、`provide()`メソッドを呼び出すことで、処理実行前に自動でバリデーションが行われます。


```ruby
service = SampleService.new('myuser', 'mypassword')
result = service.provide
```

# まとめ
`ActiveModel::Model`を利用し、コンパクトなサービス層の導入を実現しました。
また、ジェネレータも用意し、簡単にサービスクラスを追加できるようにしました。

今回作成したコードはかなり単純なものなので、もう少し改良してからgemにできたらと思います。

# 参考
[trailblazer/trailblazer: A High-Level Architecture for Ruby.](https://github.com/trailblazer/trailblazer)
[Rails：Service層を運用して良かったところ、悪かったところ - Qiita](http://qiita.com/joooee0000/items/369fd4676cd9dfb1f6eb)
[Rails のアーキテクチャ設計を考える - Qiita](http://qiita.com/kbaba1001/items/e265ad1e40f238931468)
[rails でカスタム generator 作る話 - scramble cadenza](http://mgi.hatenablog.com/entry/2014/11/29/124429)
[erikhuda/thor: Thor is a toolkit for building powerful command-line interfaces.](https://github.com/erikhuda/thor)

