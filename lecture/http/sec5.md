# 5. メッセージのルーティング
* HTTP request メッセージのルーティングは、各クライアントにより次に基づいて決定される
  * ターゲットリソース
  * クライアントのプロキシ環境設定
  * Inbound connectionの確立・再利用
* 対応するレスポンスは行きと同じ経路をたどる

## 5.1 ターゲットリソースの識別
* 多くのHTTPクライアントは、webブラウザと同様に、同一のリソースを識別するための仕組みと、 環境設定技法を用いている。
* HTTP通信は、何か目的を達成するためにUA（User Agent)からrequestが送られることで開始される。
* 目的は、requestの内容と、ターゲットリソースによって定義される。
* ターゲットリソースとは？
  * ターゲットリソース：httpリクエストを送る対象のこと
  * ターゲットリソースの識別子には大抵URI参照が使用される。
  * UAはターゲットのURIを得るために、URI参照から絶対URIに直す。
* URIについて
  * URI参照とは？
    * 絶対URIまたは相対参照とフラグメント識別子の一方または両方を組み合わせたもの
  * 絶対URIとは？
    * httpから始まる絶対pathでしていしたもの
  * 相対URIとは？
    * あるフォルダ、ファイルを起点とした相対pathで指定されたURI
  * フラグメント識別子とは？
    * URLに現れる#以降の部分。
    * フラグメント識別子以外の部分により識別されるリソースの一部分、 あるいは表現の一種を識別するために使われます

## 5.2 Inboundへの接続(ターゲット方向への)
* ターゲットURIが決まったら、クライアントは以下を決める必要がある。
  * networkによるrequestが必要か
  * 上記が必要な場合、そのrequestをどこに送るか。
* 以下の順序でrequestの送信先を決定する
  1. クライアント内のキャッシュでrequestの目的は達成できる場合、
    * この場合、送信しない
  2. プロキシによってrequestの目的は達成できる場合
    * 上記を判断するため、自身のプロキシ環境設定を検査する
    * この場合、プロキシに送信する
  3. 目的を達成できるプロキシがない場合
    * ターゲットリソースに直接アクセスする

## 5.3 requestターゲット
* 上記によって、Inbound方向への接続が得られた場合、クライアントは送信するHTTP requestメッセージにターゲットURIから導出されるrequestターゲットを伴わせる。
* requestターゲットは以下の2つによって決まる。
  * requestに使用されているメソッド
  * reqestがプロキシを介するものか
* requestターゲットは以下の4種がある。
  1. origin-form
  2. absolute-form
  3. authority-form
  4. asterisk-form
* origin-form
  * クライアントからサーバーへ向けて直接、CONNECTまたはOPTIONS requestを送る場合に使用される。
```
  http://www.example.org/where?q=now
```
  を得たいときは
```
  GET /where?q=now HTTP/1.1
  Host: www.example.org
```
2. absolute-form
  * クライアントからプロキシへ向けて、CONNECTまたはOPTIONS requestを送る場合に使用される。
```
  GET http://www.example.org/pub/WWW/TheProject.html HTTP/1.1
```
3. authority-form
  * クライアントから一つ以上のプロキシを通したトンネルを確立する時のCONNECT requestのみに使用される。
```
  CONNECT www.example.com:80 HTTP/1.1
```
4. asterisk-form
  * クライアントからOPTIONSのみを、そのサーバにおける特定の名前のリソースを指すことなくrequestする場合に使用される。
```
    OPTIONS * HTTP/1.1
```
  * また、下記の場合も使用される。
```
  OPTIONS http://www.example.org:8001 HTTP/1.1
```
  上記のrequestは最終プロキシにおいて以下のように回送される
```
  OPTIONS * HTTP/1.1
  Host: www.example.org:8001
```
  * なぜなら、プロキシは、［［［パスが空で、 query 成分がない、URIを与える absolute-formによる request-targetを伴う、OPTIONS 要請を受信したとき、自身が要請連鎖上の最後のプロキシになるならば、その要請を、指示された生成元サーバへ向けて回送するときに、 request-target として*を送信しなければならないから。

## 5.4 Host
  * Hostヘッダーは、ターゲットURIからhost&post情報を提供する。
  * 生成元サーバーが単一のIPアドレスで複数のHost名でサービスを提供している間でも、リソースを判別できるようにしている。
  * クライアントはすべてのHTTP requestメッセージ内にHostヘッダーを送信しなければならない。
  * Hostヘッダー値は重要な値なので、UAはrequest-lineの直後の最初のヘッダーとしてHostヘッダーを生成するべきである。
  ```
  GET /pub/WWW/ HTTP/1.1
  Host: www.example.org
  ```
  * クライアントは、absolute-formであってもHostヘッダーを付けて送信しなければならない。
    * なぜなら、旧式のHTTP/1.0プロキシを通しても回送できるようにするため。
  * サーバーは次のいずれかに該当する場合、400(Bad Request)で応答しなければならない。
    * HTTP/1.1 メッセージであって、Host ヘッダを欠くもの。
    * 複数個の Host ヘッダを包含するもの。
    * 包含する Host ヘッダのヘッダ値が妥当でないもの。

## 5.5 Effective Request URI()実行要請URI)
* request-target は、UAのターゲット URI の一部分のみを包含することが多いので、サーバは要請に対し適正にサービスするために、意図されたターゲットを実効要請URIとして再構築する
* 再構築ルールは省略。
* 実行要請URIの例
  * 例１：セキュアでない TCP 接続を通じて受信された、次のメッセージ
  ```
  GET /pub/WWW/TheProject.html HTTP/1.1
  Host: www.example.org:8080
  ```
  に対する実行要請URIは以下
  ```
  http://www.example.org:8080/pub/WWW/TheProject.html
  ```
  * 例2：TLS でセキュア化された TCP 接続を通じて受信された、次のメッセージ
  ```
  OPTIONS * HTTP/1.1
  Host: www.example.org
  ```
  に対する実行要請URIは以下
  ```
  https://www.example.org
  ```

## 5.6 responseとrequestの結び付け
* HTTPでは、与えられたrequestに対し、対応するresponseを結びつけるためのrequest idは存在しない。
* したがって、 responseとrequestの対応付けは時系列によって決まる。
* 1つのrequestに対して、複数のresponseを送る場合、最終メッセージ以外は1xx(Informational) responseで送る。

## 5.7 メッセージの回送
* 中継者は様々な役目を持つ。
  * パフォーマンスの向上
  * 可用性の向上
  * アクセスコントロール
  * コンテンツフィルター
* 中継者はトンネルとしてふるまわない場合、Connectionヘッダー(6.1節)を実装しなければならない。
* 中継者は自分あてのメッセージを回送してはならない。

### 5.7.1 Viaヘッダー
* Viaヘッダーは以下のものを指定する。
  * 中継プロトコルの存在。
  * requestにおいては、 UA ↔ ︎サーバ 間にある、受信者たち。
  * responseにおいては、 生成元サーバ ↔ クライアント 間にある、受信者たち

### 5.7.2 形式変換
* 一部の中継者は、メッセージとそのペイロードを形式変換するための特色機能を有している。
* 例えば、プロキシには、キャッシュ領域を節約したり、遅いリンク上の トラフィック量を抑制するために、画像形式を変換するものもある。
* メッセージを、意味論的に有意義な仕方で改変するように設計され／環境設定されている HTTP-to-HTTP プロキシは、 形式変換プロキシ と呼ばれる（改変するとは、通常の HTTP 処理に要求されるものを超えて、元の送信者にとって有意になる、 あるいは 下流の受信者にとって有意になり得るような仕方でメッセージを変更することを意味する）。 
* 例えば、形式変換プロキシには、共有 annotation サーバ（応答を、局所的 annotation データベースへの参照を内包するように 改変する）、 マルウェアフィルタ、 形式符号変換器、 プライバシーフィルタなどとして、動作しているものもあるかもしれない。
