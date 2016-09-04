# Internal DSL design in Scala
## Why Scala? 
* ()内はF#
* Flexible Syntax
  * 関数呼び出し時に.を省略できる (x)
  * セミコロンを省略できる (o)
  * 2項演算子がある (o)
  * 括弧が総略できる (o)
* 拡張可能なオブジェクトシステム
  * mix in basedなインタフェースの継承 (x)
  * 抽象型定義(子クラスでtypdefしないといけない型)、
  もしくはgenericな型パラメータ(C++で言うテンプレートパラメータ)による拡張 (o)
  * 自分型アノテーション(C++で言うthisを好きな文字列で定義できる
  * Case class(F#でいう判別共用体) (o)
* 関数型プログラミング (o)
* 静的チェック済みのダックタイピング (o)
* 静的スコープによるオープンクラス (o)
* 型推論 (o)

## Your first step toward a Scala DSL
* 新しい言語を導入するのは簡単なことではない。
クリティカルはない業務から徐々に使用していくことで進めていくことができる。
* Scalaの良い所は、JVM上で動き、Javaと一緒に使えることだ。
* Javaのシステムの補助的プロジェクトでScalaを使用していくのが良い
  * Testing Java objects
  * Scala DSL as a wrapper for Java Objects
  * Modelling noncritical functionality as a Scala DSL

## Lets DSL in Scala！
### Expressive syntax on the surface
* 表現力と冗長さの間でバランスを取らないといけない
  * 非プログラマにとって表現力豊かな言語は、プログラマにとって冗長に見える。
  * Javaの場合は表現力を上げるためには冗長になったが、Scalaの場合はもっと楽
       
           val a1 = ClientAccount(no = "acc-123", name = "John J.")
           val a2 = ClientAccount(no = "acc-234", name = "Paul M.")
           
           val accounts = List(a1, a2)
           
           val newAccounts 
               = Client Account(no = "acc-345", name = "Hugh P.") :: accounts
           
           newAccounts drop 1
    
  * 上記のコードの特徴
    * セミコロンが省略されている
    * 型推論が行われている
    * ::という2項演算子が定義されている
    * 括弧が省略されている

### Creating domain abstractions
* ScalaはOOPとFuntional Programmingの2つの力を併せ持つ
  * OOPの力
    * subtyping
    * mixin
  * FPの力
    * 関数による抽象化
    * コンビネータによる関数の組み合わせ

## Building a DSL that creates trades
* 実装に入る前に、スクリプトを見てみよう
        val fixedIncomeTrade = 200 disocunt_bonds IBM 
            for_client NOMURA on NYSE at 72.ccy(USD)
        val equityTrade = 200 equities GOOGLE
            for_client NOMURA on TKY at 10000.ccy(JPY)
* まずはコンストラクタでインスタンスを生成する方法を見よう
  * コード
* ビルダーパターンを用いることで柔軟にオブジェクトを生成できるが、
mutableなオブジェクトとオブジェクト生成の完了という2つの問題が発生する

### Implementation detail
* 元のスクリプト
        val fixedIncomeTrade = 200 disocunt_bonds IBM 
            for_client NOMURA on NYSE at 72.ccy(USD)
* 上記スクリプトに省略されているものを足してみよう
        val fixedIncomeTrade = 200.disocunt_bonds(IBM)
            .for_client(NOMURA).on(NYSE).at(72.ccy(USD))
* 上記はimplicitなビルダーパターンということができる。
* 実装をみてみよう
        type Quantity = Int
        class InstrumentHelper(qty: Quantity) {
            def discount_bonds(db: DiscountBond) (db, qty)
        }
        
        implicit def Int2InstrumentHelper(qty: Quantity) {
            new InstrumentHelper(qty)

        …
        InstrumentHelper(200).discount_bonds(IBM) -> (200, IBM)

### Variation In DSL implementation patterns
* 上記の実装を見るとパターンに気付く
  * implicit conversionを用いてn-tupleを作成している
  * これはbuilder patternと同じ
  * これは既存のbuilder patternとは違って、immutableな値を用いている
* 従来のBuilderパターンとの違い
  * mutableな自分自身を返す事はしない
  * methodが同じクラスには含まれない
  * 最後にビルダーが終了することを知らせる関数呼び出しをしなくて良い

## Modeling business rules with a DSL
### Pattern matching as an extensible Visitor
* Scalaにはcase classが存在し、パターンマッチを行うことができる
* パターンマッチを使う理由は、汎用的で拡張可能なビジターパターンを使用する
ためである。
  * OOPの場合
    * ビジターパターンを実装できるが、クラスの階層構造に埋もれがちである。
  * FPの場合
    * OOPよりも表現豊かで拡張性の
    * 高い方法で実装できる
* 次の例は、今日より以前開設された全てのクライアントアカウントの
  信用極度を今日より10%上げる話。
  * Listing 6.3はAccountの抽象化であり、具体的な実装はClientAccountと
  BrokerAccountがある。
  * Scalaでの実装例
* どうしてこれがDSLなのだろうか？
  * ドメインルールを陽に表現している
  * 小さい分野の実装である

### Enriching the domain model
* taxとfeeの計算

