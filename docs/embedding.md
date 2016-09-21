---
layout: default
title: 既存アプリケーションへの組込み
---   

既存のアプリケーションにNode-REDを組込むことが可能です。ダッシュボードアプリケーションに表示するデータのフローを作成するエディタとしてNode-REDを使用するようなシナリオが考えられます。

組込むにはNode-REDを組込みたいアプリケーションの `package.json` の `dependencies` に `node-red` を追加します。

以下の例はNode-REDをExpressアプリケーションへ組込むミニマムな構成です。

{% highlight javascript %}
var http = require('http');
var express = require("express");
var RED = require("node-red");

// Expressアプリケーションの生成
var app = express();

// 静的コンテンツのルートを追加
app.use("/",express.static("public"));

// サーバの生成
var server = http.createServer(app);

// 設定オブジェクトの生成 - 他のオプションについてはデフォルトの 'settings.js' ファイルを参照してください
var settings = {
    httpAdminRoot:"/red",
    httpNodeRoot: "/api",
    userDir:"/home/nol/.nodered/",
    functionGlobalContext: { }    // グローバルコンテキストを有効化
};

// サーバと設定とランタイムの初期化
RED.init(server,settings);

// エディタUIのルートを '/red' に指定
app.use(settings.httpAdminRoot,RED.httpAdmin);

// HTTP node UIのルートを '/api' に指定
app.use(settings.httpNodeRoot,RED.httpNode);

server.listen(8000);

// ランタイム起動
RED.start();
{% endhighlight %}

ちなみに `settings.js` で設定していた内容は `RED.init` で渡さなければいけません。したがって上記のようにベタに書くか `setting.js` を `module.exports` して `require` します。

更に以下の設定は既存アプリケーションで決められるべきなので無効化されます。

 - `uiHost`
 - `uiPort`
 - `httpAdminAuth`
 - `httpNodeAuth`
 - `httpStatic`
 - `httpStaticAuth`
 - `https`
