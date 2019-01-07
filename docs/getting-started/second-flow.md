---
layout: docs
toc: getting-started-toc.html
title: 2つ目のFlow作成
---

今回はもう少し有益なFlowを作成するために外部ソースからデータを取り込むFlowを作成します。

 - 外部Webサイトへアクセスします
 - いくつかの情報を取得します
 - 情報を読み込んで有用な状態に変換します

#### 1. Inject nodeの追加

[以前の例](first-flow)ではボタンクリックでFlowが開始されるような設定を行いました。
この例では一定間隔で自動的にFlowが開始されるように設定します。

パレットからワークスペース上にInject nodeをドラッグします。

配置したInject nodeをダブルクリックすると設定ダイアログが開きます。`every 5 minutes on every day` をセットして定期実行を設定します。

OKボタンをクリックして設定ダイアログを閉じます。

#### 2. HttpRequest nodeの追加

HttpRequest nodeを使ってWebページの内容を取得することができます。

Nodeを配置後に設定ダイアログの `URL` 項目に以下をセットします。

    https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_week.csv

Nodeの `Name` 項目には解りやすい任意の名前をセットすることができます。

これはアメリカ地質調査所のウェブサイトの、最新7日間での大きな地震のフィードです。
このフィードには様々なオプションが存在しています。詳しくはhttps://earthquake.usgs.gov/earthquakes/feed/v1.0/csv.phpをご覧ください。


#### 3. CSV nodeの追加

CSV nodeを配置してプロパティを編集し、以下のようにチェックマークを入れます。

    Input - [x] First row contains column names

#### 4. Debug nodeの追加

Debug nodeを追加します。

#### 5. すべてのNodeをワイヤーで接続

  - ワイヤーでInject nodeのOutputポートとHttpRequest nodeのInputポートを接続します
  - ワイヤーでHttpRequest nodeのOutputポートとCSV nodeのInputポートを接続します
  - ワイヤーでCSV nodeのOutputポートとDebug nodeのInputポートを接続します

#### 7. Switch nodeの追加

  - CSV nodeのOutputポートにSwitch nodeをワイヤーで接続
  - プロパティを`msg.payload.mag`に設定
  - 条件句を`>=`に、値を`7`に設定

#### 8. Change nodeの追加

  - Switch nodeのOutputポートにChange nodeをワイヤーで接続
  - ノードを`Set`とし、`msg.payload`に`PANIC!`を設定

#### 9. Debug nodeの追加

Debug nodeをChange nodeのインプットポートにワイヤーで接続


#### 10. デプロイ

この時点で、エディタにはサーバにデプロイされるべきノードのみが
存在しています。

デプロイボタンをクリックします。

サイドバーのDebugタブを選択（Ctrl-Space、またはドロップダウンメニューからDebugタブをクリック）して
Injectボタンをクリックします。次のような内容のエントリが表示されます。:

    msg.payload : Object
    {"time":"2017-11-19T15:09:03.120Z","latitude":-21.5167,"longitude":168.5426,"depth":14.19,"mag":6.6,"magType":"mww","gap":21,"dmin":0.478,"rms":0.86,"net":"us","id":"us2000brgk","updated":"2017-11-19T17:10:58.449Z","place":"68km E of Tadine, New Caledonia","type":"earthquake","horizontalError":6.2,"depthError":2.8,"magError":0.037,"magNst":72,"status":"reviewed","locationSource":"us","magSource":"us"}

それぞれのプロパティの左にある小さな矢印をクリックすることで、プロパティを開いて内容を確認できます。

マグニチュード7以上の地震があった場合、以下のような出力も確認できます。

    msg.payload : string(6)
    PANIC!

それぞれのDebug nodeの右端の緑色のボタンを利用して、特定のDebug nodeのオンとオフを切り替えることができます。

#### 11. まとめ

インターネットにアクセスして直近7日間での大きな地震リストを取得し、それぞれの詳細な情報（深さ、規模、位置など）についてJavaScriptオブジェクトに変換するFlowを作成できました。また、条件句によってマグニチュード7以上の地震を確認することができ、警告を出力することもできました。

***

#### ソース

この例で作成したFlowは以下のJSONで表されます。
JSONファイルをインポートダイアログ（Ctrl-I、またはドロップダウンメニュー）で引き渡すことで
直接インポートすることができます。


    [{"id":"e36406f2.8ef798","type":"inject","z":"f03b57d5.e525f8","name":"","topic":"","payload":"","payloadType":"str","repeat":"300","crontab":"","once":false,"x":130,"y":900,"wires":[["c3c50023.3bbed"]]},{"id":"c3c50023.3bbed","type":"http request","z":"f03b57d5.e525f8","name":"Recent Quakes","method":"GET","url":"https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_week.csv","tls":"","x":300,"y":900,"wires":[["8afc6cac.e0812"]]},{"id":"8afc6cac.e0812","type":"csv","z":"f03b57d5.e525f8","name":"","sep":",","hdrin":true,"hdrout":"","multi":"one","ret":"\\n","temp":"","x":470,"y":900,"wires":[["44779781.4190f8","6f0eb546.9e208c"]]},{"id":"44779781.4190f8","type":"debug","z":"f03b57d5.e525f8","name":"","active":true,"complete":false,"x":630,"y":900,"wires":[]},{"id":"6f0eb546.9e208c","type":"switch","z":"f03b57d5.e525f8","name":"","property":"payload.mag","propertyType":"msg","rules":[{"t":"gte","v":"7","vt":"num"}],"checkall":"true","outputs":1,"x":510,"y":960,"wires":[["d78d4aa8.8c8208"]]},{"id":"d78d4aa8.8c8208","type":"change","z":"f03b57d5.e525f8","name":"","rules":[{"t":"set","p":"payload","pt":"msg","to":"PANIC!","tot":"str"}],"action":"","property":"","from":"","to":"","reg":false,"x":650,"y":1020,"wires":[["72fddece.fac0d"]]},{"id":"72fddece.fac0d","type":"debug","z":"f03b57d5.e525f8","name":"","active":true,"complete":false,"x":750,"y":960,"wires":[]}]
