# 正規表現による文字列処理(grep, sed, awk)
## 概要
### 目的
* 正規表現を紹介し、文字列の検索、置換を効率化方法を知る
* +αとして、正規表現を扱えるlinuxコマンド(grep, sed, awk)の実用例を紹介する
* 実際に使用するところを見せることで、心理的ハードルを下げる

### 正規表現とは
* 文字列の集合を一つの文字列で表現する方法の一つ。
* 「アプリケーションソフトやプログラミングにおいて、
正規表現を用いた文字列のパターンマッチを行う機能」のことを指す場合も。[*](https://ja.wikipedia.org/wiki/%E6%AD%A3%E8%A6%8F%E8%A1%A8%E7%8F%BE)
* 使用可能なツール
  * grep, sed, awk等linuxコマンド
  * sakuraエディタ
  * Visual Studio
  
### 正規表現を扱えるLinuxコマンド
* 簡単な説明
  * grep(検索コマンド)
    * テキストファイルの中から、正規表現に一致する行を検索して出力する。
  * sed(置換コマンド)
    * テキストファイルの中から、正規表現に一致する文字列を置換して出力する。
  * awk(データ処理コマンド)
    * 任意の文字を区切りとして、テキストファイルを分割し、処理を行う
* 同じ正規表現を扱うために [*](http://www.kt.rim.or.jp/~kbk/regex/regex.html#INTERVAL)
        $ grep -E ...
        $ sed -r ...
        $ gawk --posix ...

## 正規表現
### 説明
* 対応表を手元に用意しよう
* 例：http://qiita.com/richmikan@github/items/b6fb641e5b2b9af3522e
* より表現力が豊かなERE(拡張正規表現)を使用しよう

## 正規表現を扱えるLinuxコマンド
### grep
* ファイル"blank.hpp"から"date"という文字を含む行を出力する
        $ grep -E 'date' blank.hpp
        // See http://www.boost.org for updates, documentation, and revision history.
* 拡張子がhppのファイルから"date"という文字を含むファイルとその行を出力する
        $ grep -E 'date' *.hpp
        aligned_storage.hpp:// See http://www.boost.org for updates, documentation, and revision history.
        array.hpp: * 05 Aug 2001 - minor update (Nico Josuttis)
        blank.hpp:// See http://www.boost.org for updates, documentation, and revision history.
        blank_fwd.hpp:// See http://www.boost.org for updates, documentation, and revision history.
        crc.hpp:        // bits, and update the remainder with the polynominal division
        date_time.hpp: //  See www.boost.org/libs/date_time for documentation.
        date_time.hpp:#include "boost/date_time/local_time/local_time.hpp"
        locale.hpp:#include <boost/locale/date_time.hpp>
        locale.hpp:#include <boost/locale/date_time_facet.hpp>
        operators.hpp://  20 Jun 00 Changes to accommodate Borland C++Builder 4 and Borland C++ 5.5
        rational.hpp://  05 Feb 01  Update operator>> to tighten up input syntax
* 拡張子がhppのファイルから"date"という文字を含むファイル名を出力する
        $ grep -E -l 'date' *.hpp
        aligned_storage.hpp
        array.hpp
        blank.hpp
        blank_fwd.hpp
        crc.hpp
        date_time.hpp
        locale.hpp
        operators.hpp
        rational.hpp
* 現在のフォルダ以下で"date"という文字を含むファイル名を出力する
        $ grep -E -l -r 'date'
* 現在のフォルダ以下で、拡張子がippのファイルから、
"date"という文字を含むファイル名を出力する
        $ find . -name '*.ipp' | xargs grep -E -l 'date'

### sed
* ファイル"date_time.hpp"の中身を"date"を"year"に置換した文字列を出力する
        $ sed -r 's/date/year/g' date_time.hpp
* ファイル"date_time.hpp"の中身を"date"を"year"に置換した文字列を他のファイルに書き込む
        $ sed -r 's/date/year/g' date_time.hpp > temp.hpp
* ファイル"date_time.hpp"の中身を"date"を"year"に置換する
        $ sed -r -i 's/date/year/g' date_time.hpp

### awk
* 文字区切りが"_"のcsvファイルで、各行の2列目を表示する 
        $ gawk --posix -F"_" '{print $2;}' sample.csv
        2
        4
* 文字区切りが" "のcsvファイルで、各行の行番号を先頭に付けて表示する 
        $ gawk --posix '{print "line." NR " " $0;}' sample1.csv
        line.1 1 2 hoge
        line.2 3 4 buzz
* 3列目がhogeの行だけ表示する
        $ gawk --posix '$3=="hoge"' sample1.csv
        1 2 hoge
* 正規表現/h.../を含む行の1列目、2列目を表示する
        $ gawk --posix '/h.../{print $1 " " $2;}' sample1.csv
        1 2
* 最初の行の処理前、最後の行の処理後に処理を加える
        $ gawk --posix 'BEGIN{print "***"}{print "line." NR " " $0;}END{print "***"}' sample1.csv
        ***
        line.1 1 2 hoge
        line.2 3 4 buzz
        ***

## 演習
### 演習1
* boostライブラリに対して上記を呼ぶ

### 演習2
* あるソースコードファイルがある
* 正規表現を用いて検索・置換を行ってみよう

### 演習3
* 実務で扱った例題3つ
