---
layout: default
toc: creating-nodes-toc.html
title: JavaScriptファイル
---

`.js`ファイルは、ノードのランタイム動作を定義します。

- [ノードコンストラクタ](#ノードコンストラクタ)
- [メッセージ受信](#メッセージ受信)
- [メッセージ送信](#メッセージ送信)
  - [複数出力](#複数出力)
  - [複数メッセージ](#複数メッセージ)
- [ノードを閉じる](#ノードを閉じる)
  - [タイムアウト動作](#タイムアウト動作)
- [ロギングイベント](#ロギングイベント)
  - [エラー処理](#エラー処理)
- [ステータスの設定](#ステータスの設定)
- [カスタムノードの設定](#カスタムノードの設定)
- [エディタに設定を公開する](#エディタに設定を公開する)

### ノードコンストラクタ

ノードは、ノードの新しいインスタンスを作成するために使用できるコンストラクタ関数によって定義されます。
この関数はランタイムに登録されるため、
対応するタイプのノードがフローにデプロイされたことを呼び出すことができます。

この関数には、フローエディタで設定されたプロパティを含むオブジェクトが渡されます。

最初に`RED.nodes.createNode`関数を呼び出して、全てのノードが共有する機能を初期化することが必要です。
その後、ノード固有のコードが存続します。

{% highlight javascript %}
function SampleNode(config) {
    RED.nodes.createNode(this,config);
    // node-specific code goes here

}

RED.nodes.registerType("sample",SampleNode);
{% endhighlight %}

### メッセージ受信

ノードは、`input`イベントにリスナーを登録して、
フロー内のアップストリームのノードからメッセージを受信します。

{% highlight javascript %}
this.on('input', function(msg) {
    // do something with 'msg'
});
{% endhighlight %}

### メッセージ送信

ノードは、`send`関数を使用して、フロー内のダウンストリームのノードにメッセージを送信できます:

{% highlight javascript %}
var msg = { payload:"hi" }
this.send(msg);
{% endhighlight %}

`msg`がnullの場合、メッセージは送信されません。

ノードがメッセージを受信したコトに応答してメッセージを送信している場合、
ノードは新しいメッセージオブジェクトを生成するのではなく、受信したメッセージを再利用する必要があります。
これにより、残りのフローに対してメッセージの既存のプロパティが保持されます。

#### 複数出力

ノードに複数の出力がある場合、メッセージの配列を`send`に渡すことができ、
それぞれが対応する出力に送られます。

{% highlight javascript %}
this.send([ msg1 , msg2 ]);
{% endhighlight %}

#### 複数メッセージ

この配列内のメッセージの配列を渡すことで、
特定の出力に複数のメッセージを送ることができます:

{% highlight javascript %}
this.send([ [msgA1 , msgA2 , msgA3] , msg2 ]);
{% endhighlight %}

### ノードを閉じる

新しいフローが展開されるたびに、既存のノードが削除されます。
リモートシステムとの接続を切断するなど、このような状況が発生した時に状態を整理する必要がある場合は、
`close`イベントにリスナーを登録する必要があります。

{% highlight javascript %}
this.on('close', function() {
    // tidy up any state
});
{% endhighlight %}

ノードが整理を完了するために非同期作業を行う必要がある場合、
登録されたリスナーは、
全ての作業が完了した時に呼び出される関数の引数を受け入れる必要があります。

{% highlight javascript %}
this.on('close', function(done) {
    doSomethingWithACallback(function() {
        done();
    });
});
{% endhighlight %}

*Node-RED 0.17以降*

登録されたリスナーが2つの引数を受け入れる場合、
最初は、ノードが完全に削除されたか、ただ再起動されている為にノードが閉じられているかどうかを
示すbooleanフラグとなります。

{% highlight javascript %}
this.on('close', function(removed, done) {
    if (removed) {
        // This node has been deleted
    } else {
        // This node is being restarted
    }
    done();
});
{% endhighlight %}

#### タイムアウト動作

*Node-RED 0.17以降*

Node-RED 0.17以前は、ランタイム`done`関数が呼び出されるまで待機していました。
これにより、ノードがノードを呼び出すことができなかった場合、ランタイムがハングすることになります。

0.17以降では、ランタイムは15秒以上かかるとノードをタイムアウトさせます。
エラーが記録され、ランタイムは引き続き動作します。




### ロギングイベント

ノードがコンソールに何かを記録する必要がある場合、ノードは以下の機能の１つを使用できます：

{% highlight javascript %}
this.log("Something happened");
this.warn("Something happened you should know about");
this.error("Oh no, something bad happened");

// Since Node-RED 0.17
this.trace("Log some internal detail not needed for normal operation");
this.debug("Log something more details for debugging the node's behaviour");


{% endhighlight %}


`warn`,`error`メッセージもフローエディタのデバックタブに送られます。

#### エラー処理

ノードが現在のフローを停止すべきエラーに遭遇した場合、
`this.error`関数を用いてイベントを記録する必要があります。

エラーがノードのユーザ自身の為に処理したいエラーである場合、
関数は元のメッセージ(または入力ノードの場合はからのメッセージ)を
第2引数として呼び出す必要があります:

{% highlight javascript %}
node.error("hit an error", msg);
{% endhighlight %}

これにより、同じタブにあるすべてのCatchノードがトリガーされます。

### ステータスの設定

実行中は、ノードはエディタのUIとステータス情報を共有できます。
これは`status`関数で呼び出すことでできます:

{% highlight javascript %}
this.status({fill:"red",shape:"ring",text:"disconnected"});
{% endhighlight %}

ステータスAPIの情報は[ここ](status)にあります。

### カスタムノードの設定

ノードは、ユーザの `settings.js`ファイルに設定オプションを公開したいかもしれません。

設定の名前は、次の要件に従わなければなりません:

 - 名前の前に対応するノードタイプを付ける必要があります。
 - 設定にはキャメルケースを使用する必要があります。 詳細は下記を参照してください。
 - ノードはユーザがそれを設定することを要求してはならない。
　　それはわかりやすいデフォルトでなければならない。

たとえば、ノードタイプ`sample-node`が`colour`という設定を公開した場合、
設定名は`sampleNodeColour`でなければなりません。

ランタイム内で、ノードはその設定を
`RED.setting.sampleNodeColour`として参照することができます。


#### エディタに設定を公開する

*Node-RED 0.17以降*

状況によっては、ノードがエディタに設定の値を公開したい場合があります。
ノードは`registerType`への呼び出しの一部として設定を登録しなければなりません:

{% highlight javascript %}
RED.nodes.registerType("sample",SampleNode, {
    settings: {
        sampleNodeColour: {
            value: "red",
            exportable: true
        }
    }
});
{% endhighlight %}

 - `value`フィールドは、設定に必要なデフォルト値を指定します。
 - `exportable`は、エディタで設定を利用可能にするようにランタイムに指示します。

ランタイムと同様に、
ノードはエディタ内で`RED.settings.sampleNodeColour`として設定を参照することができます。

ノードが命名要件を満たさない設定を登録しようとすると、
エラーが記録されます。
