---
layout: default
title: はじめてのFlow
---

Node-REDを[起動](running)してから、ローカルブラウザでhttp://localhost:1880を指定します。
IPアドレスまたはNode-REDインスタンスの名称がわかっていれば、以下のように別のマシンからいつでもアクセスすることができます。
http://{Node-RED-machine-ip-address}:1880

#### 1. Inject nodeの追加

Inject nodeはNode上のボタンをクリック、またはInject間隔を設定することにより、
Flowにメッセージを流すことができます。

パレットからワークスペース上にInject nodeをドラッグします。

サイドバーのInfoタブ開いて（Ctrl-Space、またはドロップダウンメニュー）、選択します。

Nodeのプロパティや振る舞いの情報を表示するには
追加したInject nodeを選択します。

#### 2. Debug nodeの追加

Debug nodeはサイドバーのDebugタブにメッセージが表示されるNodeです。
デフォルトでは `msg.payload` を表示し、
設定次第では `msg` （メッセージオブジェクト全体）を表示することもできます。

#### 3. 2つのNodeをワイヤーでつなぐ

Inject/Debug node双方のポートをドラッグして
ワイヤーでつなぐことで2つのNodeを接続します。

#### 4. デプロイ

この時点ではNodeやFlowはエディタ上にしか存在しないので
サーバーにデプロイする必要があります。

Deployボタンをクリックするとサーバにデプロイできます。

サイドバーのDebugタブを選択した状態でInjectボタンをクリックすると
Debugタブに数字が表示されるはずです。
デフォルトでInject nodeのPayloadは1970年1月1日以降のミリ秒数（タイムスタンプ）をメッセージに流します。
それでは、より有用なことをやってみましょう。

#### 5. Function nodeの追加

Function nodeには実際にJavaScriptを書くことができます。

Inject nodeとDebug nodeの間にFunction nodeを配置します。
今存在しているノードを一度削除する必要があるでしょう。（ワイヤを選択し、キーボードのDeleteキーを打鍵します。）

配置したFunction nodeをダブルクリックすると設定ダイアログが開きます。
Function項目に次のコードをコピーして貼り付けます。

{% highlight javascript %}
// Payloadから日付オブジェクトを生成
var date = new Date(msg.payload);
// 日付文字列に変換して再度Payloadをセット
msg.payload = date.toString();
// 次のNodeへmsgオブジェクトを返す
return msg;
{% endhighlight %}

OKボタンをクリックして設定ダイアログを閉じ、デプロイボタンをクリックしてデプロイします。

InjectボタンをクリックするとサイドバーのDebugタブに、
より読みやすいタイムスタンプが表示されます。

***

#### ソース

この例で作成したFlowは以下のJSONで表現されます。
このJSONをコピーしてインポートダイアログに貼り付けることで、Flowをエディタに簡単にインポートすることができます
（インポートは `Ctrl` キーを押しながら `i` キー、またはドロップダウンメニューから選択可能）。


    [{"id":"58ffae9d.a7005","type":"debug","name":"","active":true,"complete":false,"x":640,"y":200,"wires":[]},{"id":"17626462.e89d9c","type":"inject","name":"","topic":"","payload":"","repeat":"","once":false,"x":240,"y":200,"wires":[["2921667d.d6de9a"]]},{"id":"2921667d.d6de9a","type":"function","name":"Format timestamp","func":"// Create a Date object from the payload\nvar date = new Date(msg.payload);\n// Change the payload to be a formatted Date string\nmsg.payload = date.toString();\n// Return the message so it can be sent on\nreturn msg;","outputs":1,"x":440,"y":200,"wires":[["58ffae9d.a7005"]]}]
