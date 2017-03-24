---
layout: default
title: Functionノードの書き方
---

Functionノードは、受け取ったメッセージに対してJavaScriptコードを実行することができ、フローを継続するためにゼロ個以上のメッセージを返します。

メッセージは`msg`と呼ばれる一つのオブジェクトとして渡されます。このオブジェクトは慣例により、メッセージ本体を含むmsg.payload プロパティを持っています。

独自のプロパティをメッセージに追加することも可能ですが、そのことをドキュメントに記載するべきです。

* Functionの作成
* 複数の出力に送る
* 複数のメッセージを送る
* メッセージの非同期送信
* イベントのログ
* エラーの処理
* データの保存(context)
    * フローコンテクスト
    * グローバルコンテクスト
* 状態(Status)の追加
* その他のモジュールと関数

### Functionの作成

Functionノードに入力されたコードは、関数の*本体*に相当します。一番単純な関数は、メッセージを単にそのまま返します:

{% highlight javascript %}
return msg;
{% endhighlight %}

関数が`null`を返すと, メッセージは何も渡されずにフローが終了します。

返されるメッセージオブジェクトは受け取ったオブジェクトと同一である必要はありません。全く新しいオブジェクトを作って返すこともできます。例えば:

{% highlight javascript %}
var newMsg = { payload: msg.payload.length };
return newMsg;
{% endhighlight %}

<div class="doc-callout"><em>注意</em>:
新しいメッセージオブジェクトを作ると、受信した元のメッセージのあらゆるプロパティを失うことになります。これによりフローが壊れることがあり、例えば HTTP In/Response フローでは <code>msg.req</code> と <code>msg.res</code>  プロパティが最初から最後まで失われないようにする必要があります。一般論として、functionノードはどんな変更をプロパティに行っても、受け取ったメッセージオブジェクトを返す<em>べき</em>です。
</div>

#### 複数の出力に送る

functionの編集ダイアログで出力の数を変更できます。２つ以上の出力がある場合、複数の出力にメッセージを送るために、複数のメッセージを含む一つの配列を返すことができます。

これにより、条件に応じて異なる出力にメッセージを送る関数を簡単に書くことができます。 例えば、以下の例は トピックが banana なら最初の出力ではなく２番目の出力に全てを送ります:

{% highlight javascript %}
if (msg.topic === "banana") {
   return [ null, msg ];
} else {
   return [ msg, null ];
}
{% endhighlight %}

以下の例は、メッセージをそのまま最初の出力に渡し、そのメッセージのペイロードの長さを含むメッセージを２番目の出力に渡します:

{% highlight javascript %}
var newMsg = { payload: msg.payload.length };
return [msg, newMsg];
{% endhighlight %}

#### 複数のメッセージを送る

複数メッセージの配列を含む一つの配列を返すことで、一つの出力に複数のメッセージを返すことができます。一つの出力に複数のメッセージを返すと、後続のノードは返された配列の要素の順番にメッセージを一つずつ受け取ります。

以下の例では、`msg1`、`msg2`、`msg3`は最初の出力に送られます。`msg4`は２番目の出力に送られます。

{% highlight javascript %}
var msg1 = { payload:"出力１への最初の出力" };
var msg2 = { payload:"出力１への二番目の出力" };
var msg3 = { payload:"出力１への三番目の出力" };
var msg4 = { payload:"出力２への唯一のメッセージ" };
return [ [ msg1, msg2, msg3 ], msg4 ];
{% endhighlight %}

以下の例は、受信したペイロードを個別の単語に分け、それぞれの単語を順次送る一つのメッセージを返します。

{% highlight javascript %}
var outputMsgs = [];
var words = msg.payload.split(" ");
for (var w in words) {
    outputMsgs.push({payload:words[w]});
}
return [ outputMsgs ];
{% endhighlight %}

#### メッセージの非同期送信

メッセージを送信する前に何らかの非同期処理を行う必要がある場合、関数の最後で(非同期処理の結果の)メッセージを返すことはできません。

その場合、送信したいメッセージ(複数可)を `node.send()` 関数に渡して用いる必要があります。例えば:

{% highlight javascript %}
doSomeAsyncWork(msg, function(result) {
    node.send({payload:result});
});
return;
{% endhighlight %}

関数の中で非同期のコールバックを用いる場合、フローが再デプロイされる際には、処理中のリクエストや使用中のコネクションなどを後片付けする必要があります。この後片付けは、 `close`イベントハンドラを追加して行うことができます。

{% highlight javascript %}
node.on('close', function() {
    // コネクションの切断など、全ての非同期コードの後片付けをここで行う
});
{% endhighlight %}

#### イベントのログ

ノードが何かをコンソールにログ出力する必要がある場合は、以下の関数のどれかを使うことができます:

{% highlight javascript %}
node.log("何かが起きました");
node.warn("知っておくべき何かが起きました");
node.error("なんてこった、何か良くないことが起きました");
{% endhighlight %}

`warn` と `error` メッセージはフローエディタのdebugタブにも送られます。

#### エラーの処理

実行中のフローを停止させるべきエラーに遭遇した場合、関数は何もreturnするべきではありません。 同じタブのCatchノードをトリガーするために、元のメッセージを第二引数として node.error を呼ぶべきです:

{% highlight javascript %}
node.error("エラー発生", msg);
{% endhighlight %}

#### データの保存

`msg`オブジェクトとは違って、functionノードは複数回の呼び出しの間で、自分の`context`オブジェクトの中にデータを保持しておくことができます。

以下の例は関数が何回実行されているか、その回数を保持します:

{% highlight javascript %}
// もしまだカウンタが存在していなければ0に初期化
var count = context.get('count')||0;
count += 1;
// 値を書き戻して保存
context.set('count',count);
// 送信するメッセージの一部にカウントを含める
msg.count = count;
{% endhighlight %}

デフォルトではcontextのデータはNode-REDを再起動すると保持*されません*。

<div class="doc-callout"><em>注意</em>:
Node-REDのv0.13より以前のバージョンでは、contextを使う方法として、直接アクセスするやり方がドキュメントに記述されていました:
<pre>
var count = context.count;
</pre>
この方法はまだサポートされていますが非推奨であり、 <code>context.get</code>/<code>context.set</code>  を使う方法が推奨されています。これは、将来のリリースでcontextデータが永続化できるようになることを見込んでのものです。
</div>

##### フローコンテクスト

Node-RED 0.13とそれ以降のバージョンでは、`context`オブジェクトが一つのノードだけからアクセスできるのと同様に、functionノード以外のノードも含む、同一タブ上の全てのノードに共有されるフローレベルのコンテクストもあります。フローコンテクストは`flow`オブジェクト経由でアクセスされます:

{% highlight javascript %}
var count = flow.get('count')||0;
{% endhighlight %}

##### グローバルコンテクスト

全てのノードに共有されてアクセスできるグローバルコンテクストも使うことができます。例えば、キャンバスのどこからでもグローバルに使える変数 foo を作るには:

{% highlight javascript %}
global.set("foo","bar");  // これは他のノードから利用可能です
{% endhighlight %}

とし、.getで読み取ることができます。

{% highlight javascript %}
var myfoo = global.get("foo");  // これは"bar"になります
{% endhighlight %}

グローバルコンテクストはNode-REDの起動時にオブジェクトと共に前もって生成しておくこともできます。メインの*settings.js*の中の*functionGlobalContext*プロパティの中で定義することでそのように動作します。

例えば、ビルトインのosモジュールを全てのfunctionノードから利用可能にするには、以下のようにします:

{% highlight javascript %}
functionGlobalContext: {
    osModule:require('os')
}
{% endhighlight %}

この時点で、osモジュールはfunctionノードの中で`global.get('osModule')`のように参照できるようになります。

"require"が必要な外部モジュールは全てnpmでユーザディレクトリに手動インストールしなければなりません。

    cd ~/.node-red
    npm i name_of_3rd_party_module_to_be_required

<div class="doc-callout"><em>注意</em>: 
Node-RED v0.13より前のバージョンでは、グローバルコンテクストを使う方法は、以下のように<code>context</code>のサブプロパティとしてアクセスするとドキュメントに記述されていました:
<pre>
context.global.foo = "bar";
var osModule = context.global.osModule;
</pre>
この方法はまだサポートされていますが非推奨であり、<code>global.get</code>/<code>global.set</code> を使う方法が推奨されています。これは、将来のリリースでcontextデータが永続化できるようになることを見込んでのものです。
</div>

#### 状態(Status)の追加

他のノードが行えるのと同じように、functionノードも状態表示の修飾を提供することができます。状態をセットするには、`node.status`関数を呼びます。例えば

{% highlight javascript %}
node.status({fill:"red",shape:"ring",text:"切断されています"});
node.status({fill:"green",shape:"dot",text:"接続されています"});
node.status({text:"文字だけの状態"});
node.status({});   // 状態をクリアします
{% endhighlight %}

指定可能なパラメータの詳細については[Node Status documentation](https://nodered.org/docs/creating-nodes/status)を参照してください。

状態のどんな変化でも、Statusノード(Node-RED v0.12以降で利用可能)でキャッチできます。

#### その他のモジュールと関数

functionノードは以下のモジュールと関数を利用可能です:

* `Buffer` - Node.jsの`Buffer`モジュール
* `console` - Node.jsの`console`モジュール(ログ出力には`node.log`を利用する方が良いです。)
* `util` - Node.jsの`util`モジュール
* `setTimeout/clearTimeout` - javascriptのtimeout関数
* `setInterval/clearInterval` - javascriptのinterval関数

注意: functionノードは、停止もしくは再デプロイ時に、全ての未完了のタイムアウトやインターバルタイマーを自動的にクリアします。
