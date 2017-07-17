---
layout: default
toc: creating-nodes-toc.html
title: はじめてのノード開発
---

フローがデプロイされた時にノードは有効となり、フローが動作している間ノードはメッセージを送受信します。そして、次のフローがデプロイされる前に、ノードは無効になります。

ノードは、2つのファイルから構成されています。1つ目のファイルは、ノードの処理を定義したJavaScriptファイルです。2つ目のファイルは、ノードプロパティ、編集画面、ヘルプを定義したhtmlファイルです。

ノードをnpmモジュールとしてパッケージ化する時は、これらのファイルをまとめるために `package.json` ファイルを用います。

 - [簡単なノードを開発](#簡単なノードを開発)
   - [package.json](#package-json)
   - [lower-case.js](#lower-casejs)
   - [lower-case.html](#lower-casehtml)
 - [Node-REDでノードの動作確認](#Node-REDでノードの動作確認)


### 簡単なノードを開発

この例では、受け取ったメッセージの文字列を全て小文字に変換するノードの開発方法について、説明します。

まず開発用のディレクトリを作成してください。そのディレクトリの中で以下の名前のファイルを作成してください。

 - `package.json`
 - `lower-case.js`
 - `lower-case.html`

<h4 id="package-json"><i class="fa fa-file-o"></i> package.json</h4>

この `package.json` ファイルは、Node.jsモジュールの内容を記述するために標準的に使われるファイルです。

`package.json` ファイルを作成するには、 `npm init` コマンドを用います。このコマンドは、モジュール作成を手助けする質問が表示されます。モジュール名を聞かれた際は、 `node-red-contrib-example-lower-case` を入力してください。

`package.json` ファイルを作成した後は、以下の様に `node-red` セクションを追加します。

```json
{
    "name" : "node-red-contrib-example-lower-case",
    ...
    "node-red" : {
        "nodes": {
            "lower-case": "lower-case.js"
        }
    }
}
```

このセクションにより、どのノードファイルにプログラムが書かれているかを指定します。

ノードの命名規則やノードを公開する前に設定すべき他の項目など、バッケージ化の詳しい情報については、 [パッケージングガイド](packaging) を参照してください.

**注意**: このノードは例のため、npmへ公開 ***しない*** ようにしてください。

<h4 id="lower-casejs"><i class="fa fa-file-o"></i> lower-case.js</h4>

{% highlight javascript %}
module.exports = function(RED) {
    function LowerCaseNode(config) {
        RED.nodes.createNode(this,config);
        var node = this;
        node.on('input', function(msg) {
            msg.payload = msg.payload.toLowerCase();
            node.send(msg);
        });
    }
    RED.nodes.registerType("lower-case",LowerCaseNode);
}
{% endhighlight %}

ノードはモジュールとしてラップされます。Node-REDランタイムは起動時にノードを読み込み、モジュールが関数をエクスポートします。関数は引数 `RED` を指定して呼び出されます。この引数 `RED` は、モジュールがNode-REDのランタイムAPIをアクセスする機能を提供しています。

ノード自身は、 `LowerCaseNode` という関数として定義されています。この関数は、ノードの新しいインスタンスが作成される時に呼び出されます。この関数には、フローエディタで登録されたノード固有のプロパティを含むオブジェクトが渡されます。

この関数は、全てのノードで共有する機能を初期化するため、さらに `RED.nodes.createNode` 関数を呼び出します。その後、ノード固有のコードが有効になります。

このインスタンスでは、ノードは `input` イベントのリスナーを登録しています。このリスナーは、ノードにメッセージが届いた時に呼び出されます。このリスナーの中では、ペイロードの文字列を小文字に変換しています。その後、フローへメッセージを渡すため、 `send` 関数を呼び出しています。

最後に、`lower-case` というノード名を指定して `LowerCaseNode` 関数をランタイムへ登録しています。

もし、ノードのプログラムが外部モジュールと依存関係がある場合は、 `package.json` ファイルの `dependencies` セクションに依存するモジュールを記述してください。

ノードのランタイム部分についての詳しい情報は、[ここ](node-js)を参照してください。

<h4 id="lower-casehtml"><i class="fa fa-file-o"></i> lower-case.html</h4>


{% highlight html %}
<script type="text/javascript">
    RED.nodes.registerType('lower-case',{
        category: 'function',
        color: '#a6bbcf',
        defaults: {
            name: {value:""}
        },
        inputs:1,
        outputs:1,
        icon: "file.png",
        label: function() {
            return this.name||"lower-case";
        }
    });
</script>

<script type="text/x-red" data-template-name="lower-case">
    <div class="form-row">
        <label for="node-input-name"><i class="icon-tag"></i> Name</label>
        <input type="text" id="node-input-name" placeholder="Name">
    </div>
</script>

<script type="text/x-red" data-help-name="lower-case">
    <p>A simple node that converts the message payloads into all lower-case characters</p>
</script>
{% endhighlight %}    

ノードのHTMLファイルは、以下を含んでいます。

 - エディタに登録するノードの説明
 - ノードの編集テンプレート
 - ヘルプ

この例では、ノードは編集可能なプロパティとして `name` を持っています。フロー内に複数存在するノードのインスタンスを区別するのを助けるため、このプロパティは広く使われています。

ノードのエディタ部分についての詳しい情報は、[ここ](node-html)を参照してください。

### Node-REDでノードの動作確認

Once you have created a basic node module as described above, you can install
it into your Node-RED runtime.

To test a node module locally, the `npm link` command can be used. This allows you
to develop the node in a local directory and have it linked into a local node-red
install, as if it had been npm installed.

1. in the directory containing the node's `package.json` file, run: `sudo npm link`.
2. in your node-red user directory, typically `~/.node-red` run: `npm link <name of node module>`.

This creates the appropriate symbolic links between the two directories so Node-RED
will discover the node when it starts. Any changes to the node's file can be picked
up by simply restarting Node-RED.
