---
layout: default
title: Functions Nodeの書き方
---

Function nodeは前のNodeから渡されるメッセージに対して任意のコードを実行して後続のNodeへメッセージを返します。

メッセージは `msg` というJSONオブジェクトとして渡されます。慣例ではメッセージ本文を含む `msg.payload` プロパティを持ちます。一部のNodeではMQTTの名残で `msg.topic` プロパティを持ちます。

Nodeは `msg` に任意のプロパティを付与することができ、それをドキュメントに示す必要があります。

### Functionの作成

Function nodeに入力されたコードは関数の本体を表します。メッセージが到着するとコードは完全な関数宣言にラップしてから安全なサンドボックス内で実行されます。

最も簡単なFunctionは単にメッセージをそのまま返します。

{% highlight javascript %}
return msg;
{% endhighlight %}

Functionで `null` を返した場合は何もメッセージが返されずFlowが終了します。

返されるメッセージオブジェクトは渡されたオブジェクトと同一である必要はありません。Functionはメッセージを返す前に完全に新しいオブジェクトを作成することができます。

{% highlight javascript %}
var newMsg = { payload: msg.payload.length };
return newMsg;
{% endhighlight %}

<div class="doc-callout"><em>Note</em>: 新しいメッセージオブジェクトを作成すると受信したメッセージのいずれかのメッセージプロパティを失うことになります。これは一部のFLowを中断します。例えばHTTP In/Responseを用いたFlowでは<code>msg.req</code>と<code>msg.res</code>プロパティが必要です。
</div>

#### 複数Output ####

Functionの編集ダイアログでOutputの数を定義できます。複数のOutputがある場合は配列として返します。配列の各要素は各Outputに対応します。

これは条件に応じて異なるメッセージをOutputするFunctionを記述できます。例えば以下のFunctionではTopicが `banana` の場合、1番目のOutputには何も送信せず、2番目のOutputに `msg` を送信します。

{% highlight javascript %}
if (msg.topic == "banana") {
   return [ null, msg ];
} else {
   return [ msg, null ];
}
{% endhighlight %}

次の例は前の例と組み合わせることで1番目のOutputには元のメッセージを、2番目のOutputにはPayloadのメッセージ文字列長を返します。

{% highlight javascript %}
var newMsg = { payload: msg.payload.length };
return [msg, newMsg];
{% endhighlight %}

#### 複数メッセージ ####

Functionから返された配列内に更に配列を返すことにより、Outputに複数のメッセージを返すことができます。複数のメッセージがOutputとして返された場合、後続のNodeはそれらが返された順序でメッセージを受信します。次の例では `msg1`, `msg2`, `msg3`, は1番目のOutputに順次送信されます。 `msg4` は2番目のOutputに送信されます。

{% highlight javascript %}
return [ [ msg1, msg2, msg3 ], msg4 ];
{% endhighlight %}

次の例では受信したPayloadの単語を分割し、各単語を要素とした配列を返します。

{% highlight javascript %}
var outputMsgs = [];
var words = msg.payload.split(" ");
for (var w in words) {
    outputMsgs.push({payload:words[w]});
}
return [ outputMsgs ];
{% endhighlight %}

#### 非同期メッセージ送信

Functionで非同期処理を実行する場合はFunctionの終了時にメッセージを返すことができません。

その代わり非同期処理の完了時（コールバックのタイミング）に `node.send()` メソッドを利用することで後続のNodeにメッセージ（複数可）を返すことができます。

{% highlight javascript %}
doSomeAsyncWork(msg, function(result) {
    node.send({payload:result});
});
return;
{% endhighlight %}

#### イベントログ出力

Nodeがコンソールにログ出力する必要がある場合は以下のいずれかのメソッドを使用します。

{% highlight javascript %}
node.log("Something happened");
node.warn("Something happened you should know about");
node.error("Oh no, something bad happened");
{% endhighlight %}

`warn` や `error` のメッセージもエディタのDebugタブに送信されます。

#### エラーハンドリング

Functionはエラーが発生した場合にFlowを停止しつつ何かメッセージを返さなければいけません。同じシート上のCatch nodeにキャッチさせるには第2引数に元のメッセージを含めた `node.error` メソッドを呼び出す必要があります。

{% highlight javascript %}
node.error("hit an error", msg);
{% endhighlight %}

#### コンテキスト ####

Functionはmsgオブジェクトとは別にcontextオブジェクトへアクセスできます。これはFunctionの処理間でデータを共有するために使用することができます。

次の例ではFunctionが実行された回数をカウントしてcontextオブジェクトに保持しています。

{% highlight javascript %}
// まだ "context.count" プロパティが存在しない場合はカウントを初期化
context.count = context.count || 0;    
context.count += 1;
// 現在のカウント数をmsgオブジェクトの一部のプロパティとする
msg.count = context.count;
{% endhighlight %}

contextオブジェクトはNode-REDを再起動すると初期化されます。

#### グローバルコンテキスト ####

すべてのFunctionからアクセス可能なグローバルで共有されているコンテキスト。例えばエディタ上でグローバルに利用可能な変数 `foo` を作るには以下のようにします。

{% highlight javascript %}
context.global.foo = "bar";   // これは他のFunctionから利用できる
{% endhighlight %}

Node-REDの起動時に任意のオブジェクトをグローバルコンテキストに取り込むことができます。これは `settings.js` の `functionGlobalContext` プロパティで定義できます。

例えば以下のように定義するとグローバルコンテキストの `osModule` プロパティにアクセスすることでOSモジュールのすべての機能を利用することができるようになります。

{% highlight javascript %}
functionGlobalContext: {
    osModule:require('os')
}
{% endhighlight %}

Function内から上記 `osModule` プロパティにアクセスするには `context.global.osModule` のように記述します。

#### その他のオブジェクト ####

Function nodeでは次のオブジェクトが利用可能です。

* `console` - `node.log` はログ記録の好ましい方法ですがデバッグする場合は `console.log` を利用する方が有用
* `util` - Node.jsの `util` モジュール
* `Buffer` - Node.jsの `Buffer` モジュール
