# Readable Codeの内容を箇条書き

# 読みやすいコードとは？
## 読みやすいコード＝他の人が最短時間で理解できるコード

# 表面上の改善

## 名前に情報を詰め込む

### 明確な単語を選ぶ
* 例) getではなく、状況に応じてfetchやdownloadを使う
* getを使うときはアクセッサーのみで使う
* 例) stopではなく、取り消しでないものならkill、resumeできるならpauseを使う

### 汎用的な名前を避ける
* tmp、retvalなどは御法度

### スコープの大きな変数には長い名前を付ける
* 数行のコードならt、vなどを使ってもよい
* 長いコードでは短い暗号めいた名前を付けてはならない。
* そもそも長いコードは書くべきではない。スコープは短くするべき。


## 誤解されない名前をつけよう
### 最後も含む範囲を指定するときはfirst、last

### 最後を含まない範囲を指定するときはbegin、end

### bool値の名前の最初は、is、has、can、should

### ユーザーの期待に合わせよう
* getを使うときはアクセッサーのみで使う
* list::size()はO(1)

## コメントの書き方
### whatではなく、whyを書こう

### コードを書く時に考えたこと(意図)を書こう

### コードリーディングの時に感じた疑問をコードのコメントに残そう

# ループとロジックの単純化
## 制御フローを読みやすく
### 条件式の並び方
* 例) `if(10 <= length)`

### if/elseブロックの並び順
* 条件は否定形よりも肯定系
* 単純な条件を先に書く

### わかりやすくなる場合は参考演算子を使おう
* 例) `time_str += (hour >= 12) ? "pm" : "am";`

### 早期リターンを使おう

### ネストは浅くする
* 3回以上ネストしたら関数化してくくりだそう

## 巨大な式を分解しよう
* 利点
  * 巨大な式を分割できる
  * 巨大な式を変数化、関数化することで文章化できる
  * コードの主要な概念を読み手が認識しやすくなる。

# テストの読みやすさとは
## 最小のテストを作る
* ヘルパー関数やマクロを駆使することで、究極的には「こういう状況と入力から、こういう振る舞いと出力を期待する」レベルまで要約したコードを書ける。
* 例) initialize関数を作ってテスト前の準備を全部中に閉じ込める

## 独自のミニ言語を作る
* ヘルパー関数、マクロを使って独自のテスト補助関数を作ろう

## テストの入力値はできるだけ単純に
* 例) vector等のテストなら要素が0,1,2までテストすれば十分。

## 何をテストしているか