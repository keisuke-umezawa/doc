# Shellによる文字列処理
## 研究端末でのShellスクリプトの実行方法
1. Cygwinでコマンドを打って実行する
  * $ date
  * $ who
2. スクリプトファイルをCygwinから実行する
  * test.shの中身
          #!/bin/bash
          # 日付とユーザーを表示
          date
          who
    * この時、末尾が\r\n(windowsの改行)だと実行できないことがある
           $ dos2unix.exe test.sh
  * 下記コードで実行する
           $ ./test.sh
  
## Shellが解釈する記号 
1. 入出力リダイレクション
  * $ コマンド > ファイル名
  * $ コマンド < ファイル名
          $ find / -name address > findlist
2. コマンドの連携
  * $ コマンド1 | コマンド2
          $ grep TOKYO list | sort
          James      Tanaka   M    kanda   TOKYO     14
          Kouji      Haruki   M    shibuya TOKYO     34
          Takenori   Mitani   M    asakusa TOKYO     19
3. グループ化
  * $ (コマンド1 : コマンド2)
          $ ( date ; ls -l ) > lsfile
4. バックグラウンド実行
  * $ コマンド &
  
## 変数
### 変数の定義・参照・解除
     $ name=ATM
     $ echo $name
     ATM
     $ unset name

### 標準入力からの変数定義
* test.shの中身
      #!/bin/bash
      echo "名前を入力してください : \c"
      read name
      echo "こんにちは ${name}さん"

### コマンドの出力を利用した変数定義
      $ name=`whoami`
      $ echo "私は${name}です"

### 計算結果お利用した変数定義
      $ sub=`expr 5 + 1`

### 位置パラメータ
* test.shの中身
      #!/bin/bash
      echo $1
      echo $2
      echo $3
      echo $*
      echo $#
* 実行コマンド
      $ ./test.sh BEAR CAT DOG LION
      BEAR
      CAT
      DOG
      BEAR CAT DOG LION
      4

## 条件文
省略

## シェルスクリプト作成のテクニック
### 関数
* function.shの中身
      #! /bin/nash
      
      get_fullpath ()
      {
          CWD=`pwd`
          if [ -d $1 ] ; then
              cd $1
              FULLPATH=`pwd`
          elif [ -f $1 ] ; then
              cd `dirname $1`
              FULLPATH=`pwd`/`basename $1`
          fi
          echo "$FULLPATH"
          cd $CWD
      }
      echo "input file name"
      read input
      get_fullpath $input

### デバッグ
* bash -x シェルスクリプト



