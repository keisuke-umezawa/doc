# Internal DSL implementation patterns
## Filling your DSL toolbox
* DSLの開発のプロはいろんな実装方法を知っている
* 前の章で扱ったような、internal DSLに共通するパターンを覚えよう。
* Internal DSLの分類
    * Embedded  
    DSLのコード全てを陽に書く 
        * Smart API  
	fluent interfaceを使う
        * Reflective metaprogramming  
	リフレクションを用いた手法。
        * Typed embeddding  
	型を新しく定義する手法。
    * Generative  
    コンパイル時、もしくは実行時にコードの一部分(繰り返し部分など)を
    自動的に生成する
        * コンパイル時メタプログラミング
        * 実行時メタプログラミング

* このような分類はあるが、これらの手法は個別に使用されるわけではなく、組み合わされて使用される場合が多い。

## Embedded DSLs : patterns in metaprogramming
* メタプログラミングとは、本などの定義によればプログラムを書くためのプログラムを書くこと。
* しかし、これでは誤解を招きやすく、実際には「コンパイル時、実行時に
メタオブジェクトを生成するプログラミング」といえる。
* この章ではメタプログラミングのテクニックを使用した例を挙げる。
    * まずメタプログラミングの能力を持たない言語から始める。
    * 次に、メタプログラミングをするのにふさわしい言語で実装する。
* 説明の仕方
    1. サンプルのユースケースを見せる
    2. 実装レベルの構造に分解する
    3. 様々な手法を用いて実装する

### implicit context and Smart API
* DSLの表現力の指標とは
    * もしユースケースをみて理解できるなら、そのDSLは表現力が十分にある。
* contextとは
    1. 以前のコードで定義されているインスタンス
    2. 明示的、もしくは暗黙的に設定されている実行環境
* 明示的にcontextを書く例
    * List4.2のようなコードを書いた場合、以下のように明示的に
    インスタンス(acc)を書かなければならない。  
          Account acc = new Account(number);  
          acc.addHolder(hName1);  
          acc.addAddress(addr);  
    * いちいち明示的にaccと書かなくてはいけないので、読みにくくなる。
    * DSLの場合、implicitにcontextを指定できる方が良い。

* Implicitにcontxtを指定する方法
    * リフレクションを用いたメタプログラミングを利用して実現している。
          class Account
              atter_reader :no, :names, :addr, :type, :email_address
              // rest as in listing 4.1
              def self.create(&block)
                  account = Account.new
              	  account.instance_dval(&block)
                  accout
              end
          end
    * instance_evalはblockを渡して評価し、accountを生成する。
    * このコードが、Reflective メタプログラミングの例である。

### Reflective metaprogramming with dynamic decorators
* この節では、実行時メタプログラミングの別の利用法を紹介する。
    動的にインスタンスをdecorateする方法だ。 
  * decoratorパターンは実行時に動的にクラスに機能を追加するために使用する。
* Decorator in Java
  * Tradeの例を用いて始める
  * Tradeをデコレートすることでnet settlement value(NPV)の計算方法を変える
  * NPVには2つのコンポーネントが含まれる
    * gross cash value
      * 単価と量から計算できる
    * taxとfee
      * 取引国、交換地域、対象取引によって変化する
  * Listing4.5から以下のようなスクリプトを書くことができる。  
          Trade t = new CommissionDecorator(
              new TaxFeeDecorator(new Trade()));
          System.out.pringln(t.value());
* Improveint the Java implementation
  * 問題点
    * 表現力とドメインフレンドリー
      * javaでは限界
    * TradeとDecoratorに強い結びつきがある
      * 静的な継承関係をなくせば、decoratorの再利用性が高まる
    * デコレータが先で、Tradeが後になり、可読性が低い
      * 順序を反対にする
* duck typingを利用すれば、静的型安全を犠牲にして、再利用可能な抽象化が可能である
  * duck typing
    * Smalltalk、Python、Rubyなどのいくつかの動的型付けオブジェクト
    指向プログラミング言語に特徴的な型付けの作法のことである。
    * それらの言語ではオブジェクト（変数の値）に何ができるかは
    オブジェクトそのものが決定する。
    * つまり、オブジェクトがあるインタフェースのすべての
    メソッドを持っているならば、たとえそのクラスがそのインタフェースを
    宣言的に実装していなくとも、オブジェクトはそのインタフェースを実行時に
    実装しているとみなせる、ということである。
    * それはまた、同じインタフェースを実装するオブジェクト同士が、
    それぞれがどのような継承階層を持っているのかということと無関係に、
    相互に交換可能であるという意味でもある。
    * C++のtemplateはダック・タイピングの静的版である
* Dynamic decorators in Ruby
  * withメソッドのみが追加されている
  * Tradeの説明をする前に、decoratorを見よう
    * decoratorはmixinとして実装されている
    * また、decoratorは静的にTradeクラスと結びついていない
      * duck typingを用いているので、
      valueメソッドを実装した全てのクラスに対してdecorateすることができる
      * superによって親クラスのvalueを次々に呼び出すようになっている。
  * withメソッドは引数にdecoraorを取り、インスタンスを作る
    * この時に動的メタプログラミングが使用されている
    * 実行時に望むインスタンスを作成できるため以下の効果がある
      * 再利用可能
      * 疎結合
  * 上記のコードから以下のようなスクリプトを書くことができる
          tr = Trade.new('r-123', 'a-123', 'i-123, 2000)
              .with TaxFee, Comission
          puts tr.value
  * 実行時メタプログラミングのメリット
    * 簡潔になる
    * 表現力が増す
    * 動的にインスタンスを操作できる
  * デメリット
    * 静的型安全ではなくなる
    * 実行速度が遅くなる

## Embedded DSLs : patterns with typed abstractions
* 型システムを用いたDSLの作成について

### Higher order functions as generic abstractions
* この章では、金融商品の取引活動のドキュメント作成の例を扱う。
* グループ分けされたレポートの例
  * 図4.7はあるクライアントの取引報告書の例で以下の内容が含まれる
    * 銘柄
    * 量
    * 取引時刻
    * 総額
  * 図4.8は銘柄によってグループ分けしたもの
  * 図4.9は量によってグループ分けしたもの
* このように、様々な値を用いてグループ分けしたいことがある
* これを実現するには以下の2つの方法がある
  * それぞれのグループ分けを別の関数で実装する
  * groupByというコンビネータを作り、高階関数で分類方法を与える
* Scalaの覚えるべき文法
  * case class : F#のレコード体のようなもの
  * 暗黙の型変換
  * For-comprehensions : 以下のように書くことができる
          for {
              list <- nestedList
              num <- list
          } print(num)
  * 高階関数
* Setting UP
  * DSLでは以下のように書きたい
          activityReport groupBy(_.instrument)
          activityReport groupBy(_.quantity)
  * p.108のコードの説明
    1. 型の定義
      * ドメインの物を型で定義するのは重要
        * コードが自分のことを説明してくれるから
        * ドメインユーザに優しいだから
        * Instrumentの型を後で変更するなどの保守が楽だから
    2. TradeQuantityはcase class
    3. 暗黙の型変換
    4. 報告書で最も重要な抽象化された型
* それぞれ別々に実装する方法
* groupeByで実装する方法

### Using explicit type constraints to model domain logic 
* この章では、以下のそれぞれの言語におけるビジネスロッジクの制限の
チェック方法を説明する
  * 動的型付言語
    * 実行時に型が決まるので、実行時に正しい型かチェックしなくてはならない
    * 同じチェックを実行時に何度も行う必要がある
    * 実行時のチェックが正しく行われているか単体テストを書かなくてはならない
  * 静的型付言語
    * コンパイル時に正しい型かチェックされるので、
    実行にチェックする必要はない
