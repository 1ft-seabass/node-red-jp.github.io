---
layout: default
title: Functionノードの書き方
---

Functionノードは、受け取ったメッセージに対してJavaScriptコードを実行することができ、
フローを継続するためにゼロ個以上のメッセージを返します。

メッセージは`msg`と呼ばれる一つのオブジェクトとして渡されます。
このオブジェクトは慣例により、メッセージ本体を含む`msg.payload`プロパティを持っています。

独自のプロパティをメッセージに追加することも可能ですが、
そのことをドキュメントに記載するべきです。

 - [Functionの作成](#writing-a-function)
 - [複数の出力に送る](#multiple-outputs)
 - [複数のメッセージを送る](#multiple-messages)
 - [メッセージの非同期送信](#sending-messages-asynchronously)
 - [イベントのログ](#logging-events)
 - [エラーの処理](#handling-errors)
 - [データの保存(context)](#storing-data)
    - [複数の値の取得/設定](#getset-multiple-values)
    - [コンテキストへの非同期なアクセス](#asynchronous-context-access)
    - [複数のコンテキストストア](#multiple-context-stores)
    - [globalコンテキスト](#global-context)
 - [ステータスの追加](#adding-status)
 - [追加モジュール読み込み](#loading-additional-modules)
 - [APIリファレンス](#api-reference)
    - [その他のモジュールと関数](#other-modules-and-functions)

### Functionの作成

Functionノードに入力されたコードは、関数の*本体*に相当します。
最も単純な関数は、メッセージを単にそのまま返す場合です。:

{% highlight javascript %}
return msg;
{% endhighlight %}

関数が`null`を返すと、メッセージは何も渡されずにフローが終了します。

返されるメッセージオブジェクトは受け取ったオブジェクトと同一である必要はありません。
全く新しいオブジェクトを作って返すこともできます。
以下がその例です。:

{% highlight javascript %}
var newMsg = { payload: msg.payload.length };
return newMsg;
{% endhighlight %}

<div class="doc-callout"><em>Note</em> : 新しいメッセージオブジェクトを生成すると、
受信した元のメッセージのあらゆるプロパティを失うことになります。これによりフローが壊れることがあり、
例えば HTTP In/Response フローでは <code>msg.req</code> と <code>msg.res</code>  プロパティが
最初から最後まで失われないようにする必要があります。
一般論として、functionノードはどんな変更をプロパティに行っても、
受け取ったメッセージオブジェクトを返す<em>べき</em>です。</div>

#### 複数の出力に送る

Functionノードの編集ダイアログで出力の数を変更することができます。
2つ以上の出力がある場合、複数の出力にメッセージを送るために、
複数のメッセージを含む一つの配列を返すことができます。

これにより、条件に応じて異なる出力にメッセージを送る関数を簡単に書くことができます。 
例えば、以下の例はtopicが`banana`なら最初の出力ではなく
2番目の出力に全てを送ります。:

{% highlight javascript %}
if (msg.topic === "banana") {
   return [ null, msg ];
} else {
   return [ msg, null ];
}
{% endhighlight %}

以下の例は、入力されたメッセージをそのまま最初の出力に渡し、
そのメッセージのpayloadの長さを含むメッセージを２番目の出力に渡します。:

{% highlight javascript %}
var newMsg = { payload: msg.payload.length };
return [msg, newMsg];
{% endhighlight %}

#### 複数のメッセージを送る

複数メッセージの配列を含む一つの配列を返すことで、
一つの出力に複数のメッセージを返すことができます。
一つの出力に複数のメッセージを返すと、
後続のノードは返された配列の要素の順番にメッセージを一つずつ受け取ります。

以下の例では、`msg1`、`msg2`、`msg3`は最初の出力に送られます。
`msg4`は２番目の出力に送られます。

{% highlight javascript %}
var msg1 = { payload:"first out of output 1" };
var msg2 = { payload:"second out of output 1" };
var msg3 = { payload:"third out of output 1" };
var msg4 = { payload:"only message from output 2" };
return [ [ msg1, msg2, msg3 ], msg4 ];
{% endhighlight %}

以下の例は、受信したpayloadを個別の単語に分け、
それぞれの単語を順次送る一つのメッセージを返します。

{% highlight javascript %}
var outputMsgs = [];
var words = msg.payload.split(" ");
for (var w in words) {
    outputMsgs.push({payload:words[w]});
}
return [ outputMsgs ];
{% endhighlight %}

#### メッセージの非同期送信

メッセージを送信する前に何らかの非同期処理を行う必要がある場合、
関数の最後で(非同期処理の結果の)メッセージを返すことはできません。

その場合、送信したいメッセージ(複数可)を `node.send()` 関数に渡して用いる必要があります。
以下がその例です。:

{% highlight javascript %}
doSomeAsyncWork(msg, function(result) {
    node.send({payload:result});
});
return;
{% endhighlight %}

関数の中で非同期のコールバックを用いる場合、
フローが再デプロイされる際に処理中のリクエストや使用中のコネクションなどを後片付けする必要があります。
この後片付けは、`close`イベントハンドラを追加することで行うことができます。

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

より詳細なログには、`node.trace()`および`node.debug()`が利用できます。
これらのレベルを出力するようにログが設定されていない場合、ログには出力されません。

#### エラーの処理

実行中のフローを停止させるべきエラーに遭遇した場合、関数は何も返却するべきではありません。
同じタブのCatchノードをトリガーするために、
元のメッセージを第2引数として`node.error` を呼ぶべきです。:

{% highlight javascript %}
node.error("エラー発生", msg);
{% endhighlight %}

#### データの保存

`msg`オブジェクトとは違って、functionノードは複数回の呼び出しの間で、自分の`context`オブジェクトの中にデータを保持しておくことができます。

Node-REDのコンテキストについての詳細な情報は[こちら](user-guide/context)で確認することができます。

Functionノードの場合、
コンテキストにアクセスすることができる3つの定義済み変数が存在します。:

   - `context` - ノードスコープのローカルなコンテキスト
   - `flow` - フロースコープのコンテキスト
   - `global` - グローバルスコープのコンテキスト
   
以下の例では`flow`コンテキストを利用しますが、
`context`および`global`でも同様に利用できます。

<div class="doc-callout"><em>Note</em> : これらの定義済み変数はFunctionノードの特徴です。
ノードを自作する場合は、contextへのアクセス方法は<a href="/docs/creating-nodes/context">ノード作成ガイド</a>を参照してください。</div>


コンテキストへのアクセスには、同期または非同期の2種類のモードがあります。
既存のコンテキストストアは両方のモードを提供しています。いくつかのストアは非同期アクセスのみ提供しており、
同期してアクセスがおこなわれるとエラーを発生させます。

contextから値を取得するには以下のようにします。:

{% highlight javascript %}
var myCount = flow.get("count");
{% endhighlight %}

値を設定するには以下のようにします。:

{% highlight javascript %}
flow.set("count", 123);
{% endhighlight %}
   
以下の例は関数が何回実行されているか、
その回数を保持します。:

{% highlight javascript %}
// もしまだカウンタが存在していなければ0に初期化
var count = context.get('count')||0;
count += 1;
// 値を書き戻して保存
context.set('count',count);
// 送信するメッセージの一部にカウントを含める
msg.count = count;
return msg;
{% endhighlight %}

##### 複数の値の取得/設定

Node-RED 0.19以降、複数の値の取得または設定を1回でできるようになっています。:

{% highlight javascript %}
// Node-RED 0.19以降
var values = flow.get(["count", "colour", "temperature"]);
// values[0]には'count'の値が格納される
// values[1]には'colour'の値が格納される
// values[2]には'temprature'の値が格納される
{% endhighlight %}

{% highlight javascript %}
// Node-RED 0.19以降
flow.set(["count", "colour", "temperature"], [123, "red", "12.5"]);
{% endhighlight %}

この方法では、値が見つからない場合には`null`が設定されます。


##### コンテキストへの非同期なアクセス

コンテキストストアでのデータ保管に非同期なアクセスが必要な場合、
`get`および`set`関数はコールバック引数が必須です。

{% highlight javascript %}
// 単一の値を取得
flow.get("count", function(err, myCount) { ... });

// 複数の値を取得
flow.get(["count", "colour"], function(err, count, colour) { ... })

// 単一の値を設定
flow.set("count", 123, function(err) { ... })

// 複数の値を設定
flow.set(["count", "colour", [123, "red"], function(err) { ... })
{% endhighlight %}

コールバック関数に渡される第1引数`err`は、
contextにアクセスする際にエラーが発生する場合のみセットします。

先述のカウンタの例を、非同期におこなうと以下のようになります。:

{% highlight javascript %}
context.get('count', function(err, count) {
    if (err) {
        node.error(err, msg);
    } else {
        // もしまだカウンタが存在していなければ0に初期化
        count = count || 0;
        count += 1;
        // 値を書き戻して保存
        context.set('count',count, function(err) {
            if (err) {
                node.error(err, msg);
            } else {
                // 送信するメッセージの一部にカウントを含める
                msg.count = count;
                // メッセージの送信
                node.send(msg);
            }
        });
    }
});
{% endhighlight %}

##### 複数のコンテキストストア

0.19版では、複数のコンテキストストアに値を格納できます。
例として、`memory`および`file`といったストアの両方を利用することができます。

`get`/`set`関数は、利用するストアを特定するため、
任意の引数を利用することができます。

{% highlight javascript %}
// 値の同期取得
var myCount = flow.get("count", storeName);

// 値の非同期取得
flow.get("count", storeName, function(err, myCount) { ... });

// 値の同期設定
flow.set("count", 123, storeName);

// 値の非同期設定
flow.set("count", 123, storeName, function(err) { ... })
{% endhighlight %}


##### globalコンテキスト

globalコンテキストは、Node-RED起動時にオブジェクトを予め格納しておくことができます。
オブジェクトは、*settings.js*ファイルの`functionGlobalContext`プロパティで
定義することができます。

このテクニックはFunctionノード内で追加モジュールのロードに
利用することができます。

#### ステータスの追加

他のノードが行えるのと同じように、functionノードもステータス表示を装飾することができます。
ステータスをセットするには、`node.status`関数を呼びます。
例えば

{% highlight javascript %}
node.status({fill:"red",shape:"ring",text:"切断されています"});
node.status({fill:"green",shape:"dot",text:"接続されています"});
node.status({text:"文字だけの状態"});
node.status({});   // 状態をクリアします
{% endhighlight %}

指定可能なパラメータの詳細については
[Nodeのステータス](creating-nodes/status)を参照してください。

ステータスのどんな変化でも、Statusノードでキャッチできます。

#### 追加モジュールのロード

Functionノードに追加モジュールを直接ロードすることはできません。
*settings.js*ファイルでロードし、
`functionGlobalContext`プロパティに追加される必要があります。

例えば、`os`組込みモジュールは*settings.js*ファイルに以下の設定を追加することによって、
すべてのFunctionノードで利用可能になります。

{% highlight javascript %}
functionGlobalContext: {
    osModule:require('os')
}
{% endhighlight %}

この時点で、モジュールはFunctionノード内で以下のように参照できます。
`global.get('osModule')`

設定ファイルでロードされたモジュールは、設定ファイルと同じディレクトリにインストールされる必要があります。
デフォルトのユーザディレクトリ（`~/.node-red`）を利用しているだろうほとんどのユーザは以下のようにインストールを実行します。:

    cd ~/.node-red
    npm install name_of_3rd_party_module

***

### APIリファレンス

Functionノード内では以下のオブジェクトが利用できます。

#### `node`
 * `node.id` : FunctionノードのID - *0.19で導入*
 * `node.name` : Functionノードの名前 - *0.19で導入*
 * `node.log(..)` : [メッセージをログ出力する](#logging-events)
 * `node.warn(..)` : [warnレベルでメッセージをログ出力する](#logging-events)
 * `node.error(..)` : [errorレベルでメッセージをログ出力する](#logging-events)
 * `node.debug(..)` : [debugレベルでメッセージをログ出力する](#logging-events)
 * `node.trace(..)` : [traceレベルでメッセージをログ出力する](#logging-events)
 * `node.on(..)` : [イベントハンドラを登録する](#sending-messages-asynchronously)
 * `node.status(..)` : [ノードの状態を更新する](#adding-status)
 * `node.send(..)` : [メッセージを送信する](#sending-messages-asynchronously)

#### `context`
 * `context.get(..)` : ノードスコープコンテキストからプロパティ値を取得する
 * `context.set(..)` : ノードスコープコンテキストにプロパティ値を設定する
 * `context.keys(..)` : すべてのノードスコープコンテキストのプロパティキーの一覧を取得する
 * `context.flow` : `flow`と同義です
 * `context.global` : `global`と同義です

#### `flow`
 * `flow.get(..)` : フロースコープコンテキストからプロパティ値を取得する
 * `flow.set(..)` : フロースコープコンテキストにプロパティ値を設定する
 * `flow.keys(..)` : すべてのフロースコープコンテキストのプロパティキーの一覧を取得する

#### `global`
 * `global.get(..)` : グローバルスコープコンテキストからプロパティ値を取得する
 * `global.set(..)` : グローバルスコープコンテキストにプロパティ値を設定する
 * `global.keys(..)` : すべてのグローバルスコープコンテキストのプロパティキーの一覧を取得する

#### `RED`
 * `RED.util.cloneMessage(..)` : メッセージオブジェクトを再利用できるように安全にクローンします  

#### その他のモジュールと関数

functionノードは以下のモジュールと関数を利用可能です。:

* `Buffer` - Node.jsの`Buffer`モジュール
* `console` - Node.jsの`console`モジュール(ログ出力には`node.log`を利用する方が良いです。)
* `util` - Node.jsの`util`モジュール
* `setTimeout/clearTimeout` - javascriptのtimeout関数
* `setInterval/clearInterval` - javascriptのinterval関数

Note: functionノードは、停止もしくは再デプロイ時に、
全ての未完了のタイムアウトやインターバルタイマーを自動的にクリアします。
