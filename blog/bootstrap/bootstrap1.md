# Bootstrapを用いて社内アプリのインタフェースをつくったみた！
# イントロ
## 目的
* 日々の業務でもどうせなら流行りのwebアプリにのったものを使いたい
  * 見た目の美しいインタフェース
  * 自由自在で使い勝手の良いインタフェース
  * 端末に関係なく使えるwebアプリ  

## 問題点
* エクセルツールとかいうダサいインタフェース。
* UI用ライブラリ使ったアプリもあるけど依然としてダサいし、保守がめんどうだ

## 問題点
* 見た目が美しくなく良いユーザエクスペリエンスが期待できない
* 表計算ツールをインタフェースにするというだささ
* 端末依存甚だしい

## 解決策
* 更新が容易なweb UIスキームを用いて、UIを作ろう！
  * なぜなら、
    1. 見た目が美しい
    2. 更新が容易
    3. 端末依存がない

# Bootstrapとは
## Bootstrap 
* Twitter社が開発したCSSのフレームワーク。
  * その他CSSフレームワークとして以下の様なものがある 
  [*](http://tech.eversense.co.jp/1023)
    1. Material UI(Google社)
    2. Pure(Yahoo!社)
    3. Web Start Kit(Google社)
    4. foundation
    5. SkyBlue CSS
  * 上記で一番メジャーなものがBootstrapだそう
* メリットは？
  * レスポンシブルなwebデザインに対応している
  [*](http://techacademy.jp/magazine/6270)
    * CSS3のメディアクエリを利用して、
    レスポンシブルにブラウザの横幅サイズを変更できる
  * 追加要件に容易に対応できる
  [*](http://www.moongift.jp/2014/01/special1-bootstrap/)
  * 全体のデザインのバランスが崩れない
  [*](http://www.moongift.jp/2014/01/special1-bootstrap/)  

# Bootstrapを使ってみよう  
## 準備
* この[ページ](http://techacademy.jp/magazine/6270)に従って準備を行った

## Fx Calculatorをつくってみた
* 現状
  * 依然としてダサいがまあデザインは後で改良するとしてこんな感じ


