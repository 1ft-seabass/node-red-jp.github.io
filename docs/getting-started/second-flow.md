---
layout: default
title: Flow作成
---

今回はもう少し有益なFlowを作成するために外部ソースからデータを取り込むFlowを作成します。

 - 外部Webサイトへアクセスします
 - いくつかの情報を取得します
 - 情報を読み込んで有用な状態に変換します
 - 取得した情報のJSONオブジェクトとモノのon/offを表すBoolean値の2種類を出力します

#### 1. Inject nodeの追加

[以前の例](first-flow.html)ではボタンクリックでFlowが開始されるような設定を行いました。この例では一定間隔で自動的にFlowが開始されるように設定します。

パレットからワークスペース上にInject nodeをドラッグします。

配置したInject nodeをダブルクリックすると設定ダイアログが開きます。`every 5 minutes on every day` をセットして定期実行を設定します。

OKボタンをクリックして設定ダイアログを閉じます。

#### 2. HttpRequest nodeの追加

HttpRequest nodeを使ってWebページの内容を取得することができます。

Nodeを配置後に設定ダイアログの `URL` 項目に以下をセットします。

    http://realtimeweb-prod.nationalgrid.com/SystemData.aspx

Nodeの `Name` 項目には解りやすい任意の名前をセットすることができます。

#### 3. Function nodeの追加

Function nodeを配置して以下のコードをセットします。

{% highlight javascript %}
// HTTP出力文字列からイギリスの電力需要/周波数/時間をテキスト抽出解析

if (~msg.payload.indexOf('<span')) {
    var dem = msg.payload.split('Demand:')[1].split("MW")[0];
    var fre = msg.payload.split('Frequency:')[1].split("Hz")[0];

    msg.payload = {};
    msg.payload.demand = parseInt(dem.split(">")[1].split("<")[0]);
    msg.payload.frequency = parseFloat(fre.split(">")[1].split("<")[0]);

    msg2 = {};
    msg2.payload = (msg.payload.frequency >= 50) ? true : false;

    return [msg,msg2];
}
return null;
{% endhighlight %}

Function nodeの出力数を<b>2</b>にします。

#### 4. Debug nodeの追加

Debug nodeを2つ追加します。

#### 5. すべてのNodeをワイヤーで接続

  - ワイヤーでInject nodeのOutputポートとHttpRequest nodeのInputポートを接続します
  - ワイヤーでHttpRequest nodeのOutputポートとFunction nodeのInputポートを接続します
  - ワイヤーでFunction nodeの2つのOutputポートと各Debug nodeのInputポートを接続します

#### 6. デプロイ

この時点ではNodeやFlowはエディタ上にしか存在しないのでサーバーにデプロイする必要があります。

Deployボタンをクリックするとサーバにデプロイできます。

サイドバーのDebugタブを選択してInjectボタンをクリックします。次のような内容のエントリが表示されます。

    (Object) { "demand": 34819, "frequency": 50.04 }

さらに次のような別の内容のエントリも出力されます。

    (boolean) true


#### 7. まとめ

これでイギリスの総電力消費量をWeb経由で取得し、電力需要と周波数をJavaScriptオブジェクトに変換しました。

このオブジェクトはFunction nodeの1つ目のOutputから出力されます。

この周波数は全体的な負荷指標です。周波数が50Hz未満の場合、ナショナルグリッド社全体に余分な負荷があるかもしれません。これはFunction nodeの2つ目のOutputから出力されたメッセージが示しています。Payloadが `true` の場合、グリッドの容量があります。

***

#### ソース

この例で作成したFlowは以下のJSONで表現されます。このJSONをコピーしてインポートダイアログに貼り付けることで、Flowをエディタに簡単にインポートすることができます（インポートは `Ctrl` キーを押しながら `i` キー、またはドロップダウンメニューから選択可能）


    [{"id":"11b032a3.ee4fcd","type":"inject","name":"Tick","topic":"","payload":"","repeat":"","crontab":"*/5 * * * *","once":false,"x":161,"y":828,"z":"6480e14.f9b7f2","wires":[["a2b3542e.5d4ca8"]]},{"id":"a2b3542e.5d4ca8","type":"http request","name":"UK Power","method":"GET","url":"http://realtimeweb-prod.nationalgrid.com/SystemData.aspx","x":301,"y":828,"z":"6480e14.f9b7f2","wires":[["2631e2da.d9ce1e"]]},{"id":"2631e2da.d9ce1e","type":"function","name":"UK Power Demand","func":"// does a simple text extract parse of the http output to provide an\n// object containing the uk power demand, frequency and time\n\nif (~msg.payload.indexOf('<span')) {\n    var dem = msg.payload.split('Demand:')[1].split(\"MW\")[0];\n    var fre = msg.payload.split('Frequency:')[1].split(\"Hz\")[0];\n\n    msg.payload = {};\n    msg.payload.demand = parseInt(dem.split(\">\")[1].split(\"<\")[0]);\n    msg.payload.frequency = parseFloat(fre.split(\">\")[1].split(\"<\")[0]);\n    \n    msg2 = {};\n    msg2.payload = (msg.payload.frequency >= 50) ? true : false;\n\n    return [msg,msg2];\n}\n\nreturn null;","outputs":"2","valid":true,"x":478,"y":828,"z":"6480e14.f9b7f2","wires":[["8e56f4d3.71a908"],["cd84371b.327bc8"]]},{"id":"8e56f4d3.71a908","type":"debug","name":"","active":true,"complete":false,"x":678,"y":798,"z":"6480e14.f9b7f2","wires":[]},{"id":"cd84371b.327bc8","type":"debug","name":"","active":true,"complete":false,"x":679,"y":869,"z":"6480e14.f9b7f2","wires":[]}]
