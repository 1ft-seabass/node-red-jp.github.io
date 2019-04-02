---
layout: default
title: 設定
---
Node-REDを設定するために以下のプロパティが利用できます。

スタンドアローンのアプリケーションとして実行すると、これらのプロパティは`settings.js`ファイルから読み込まれます。
このファイルの場所は以下の順に決定されます。

 - 起動時に`--settings|-s`オプションで指定した場所
 - 起動時に`--userDir|-u`オプションで指定されたディレクトリ
 - デフォルトのユーザディレクトリ`$HOME/.node-red/settings.js`
 - Node-REDがインストールされたディレクトリ

Node-REDにはユーザが指定する`settings.js`が存在しない場合に
使用するデフォルトの`settings.js`ファイルが含まれています。
また、独自の`settings.js`の雛形として使用できます。
[GitHub上](https://github.com/node-red/node-red/blob/master/packages/node_modules/node-red/settings.js)でも確認できます。

<div class="doc-callout">
<em>Note</em> :  <code>settings.js</code>ファイルは<em>JavaScript object</em>をエクスポートします。Node-REDを設定するため、キー/バリューのペアを新規に追加または既存のものを変更することによってJavaScript objectを変更する方法を理解する必要があります。
</div>

[既存アプリケーションへの組込み](embedding)の場合、
`settings`プロパティは`RED.init()`の呼び出しでNode-REDへ渡されます。
ただ、このモードで実行すると特定のプロパティは無視されます。

### Node-RED実行時の設定

flowFile
: Flow設定をファイルに保存する場合のファイル名です。デフォルト: `flows_<hostname>.json`

userDir
: Flow設定、資格情報ファイルおよびすべてのライブラリデータなどすべてのユーザデータを保存するためのディレクトリのパスです。デフォルト: `$HOME/.node-red`

nodesDir
: 自作のNodeなど、追加でイントールしたノードを探索するディレクトリのパス。Node-REDは*userDir*ディレクトリ以下の`nodes`ディレクトリを探索します。このプロパティはNode-REDのインストール機構以外からインストールされたノードなどのため、Node-REDが探索をおこなう追加のディレクトリを指定します。
  ここで指定したディレクトリ内にNodeの`.js`と`.html`ファイルを配置するとNode-REDのパレットに表示されるようになります。Node-REDの外部のパスも指定できます。
  デフォルト: `$HOME/.node-red/nodes`

uiHost
: 接続を待機するホストインタフェースです。デフォルト: `0.0.0.0` -
  *all IPv4 interfaces*.

  *Standalone only*.

uiPort
: エディタUIを提供するために利用するポート番号です。デフォルト: `1880`.

  *Standalone only*.

httpAdminRoot
: エディタUIのためのルートURLです。`false`が設定されている場合、すべての管理エンドポイントが無効になります。
  これにはAPIエンドポイントとエディタUIの両方が含まれます。エディタUIのみを無効にするためには、以下の`disableEditor`プロパティを参照してください。デフォルト: `/`

httpAdminAuth
: *非推奨*: `adminAuth`を参照してください。

  エディタUIのHTTPベーシック認証を有効にします。:

      httpAdminAuth: {user:"nol", pass:"5f4dcc3b5aa765d61d8327deb882cf99"}

  `pass`プロパティは実際のパスワードのmd5ハッシュ値を指定します。上記の例では実際にBasic認証ダイアログへ入力するパスワードは`password`という文字列になります。以下のコマンドを実行することによってハッシュ値が得られます。:

      node -e "console.log(require('crypto').createHash('md5').update('YOUR PASSWORD HERE','utf8').digest('hex'))"

  *Standalone only*.

httpNodeRoot
: HTTP In NodeのHTTPエンドポイントのルートURLを指定できます。`false`が設定されている場合、すべてのノードのHTTPエンドポイントは無効になります。デフォルト: `/`

httpNodeAuth
: HTTP In NodeのHTTPエンドポイントにBasic認証を設けます。書式は`httpAdminAuth`を参照してください。

httpRoot
: 管理およびNodeのエンドポイントの両方のルートURLを設定します。`httpRoot`を指定すると`httpAdminRoot`と`httpNodeRoot`を同じパスで上書きします。

https
: [こちら](http://nodejs.org/api/https.html#https_https_createserver_options_requestlistener)で定義されている指定されたoptionsオブジェクトでHTTPSを有効にします。

  *Standalone only*.

disableEditor
: `true`を指定するとFlowエディタが無効になります。`httpAdminRoot`を`false`にした場合はUIもAPIも無効になりますが、こちらはUIのみ無効になります。デフォルト: `false`

httpStatic
: 静的ウェブコンテンツの提供元となるローカルディレクトリのパスです。
  コンテンツはトップレベルURL、つまり`/`から提供されます。例えば`/home/username/.node-red/`と指定した場合、`/home/username/.node-red/index.html`を作成すると`/`へアクセスすると作成した`index.html`が表示されます。
  `httpStatic`を指定する場合、`/`ではないパスでエディタUIを利用できるようにするため、`httpAdminRoot`は`/`より下層のパスにする必要があります。

  *Standalone only*.

httpStaticAuth
: 静的コンテンツにBasic認証を設けます。書式は`httpAdminAuth`を参照してください。

httpNodeCors
: HTTP In NodeのHTTPエンドポイントのCross-Origin Resource Sharing (CORS) を有効にします。詳細は[こちら](https://github.com/troygoode/node-cors#configuration-options)。
  以下が設定例です。

      httpNodeCors: {
        origin: "*",
        methods: "GET,PUT,POST,DELETE"
      },

httpNodeMiddleware
: HTTP In NodeのHTTPエンドポイントにHTTPミドルウェアを追加できます。これにより、認証など、Nodeに必要なカスタム処理がすべて可能になります。
  ミドルウェア機能の形式は[こちら](http://expressjs.com/guide/using-middleware.html#middleware.application)で文書化されています。
  HTTPエンドポイントでセッションとクッキーのハンドラを利用したい場合は以下のように設定します。

      httpNodeMiddleware: function(req,res,next) {
          // Perform any processing on the request.
          // Be sure to call next() if the request should be passed
          // to the relevant HTTP In node.
      }

logging
:  現在コンソールログのみがサポートされています。以下のオプションでロギングの様々なレベルを指定することができます。

 - **fatal** - アプリケーションが使用できなくなるような致命的エラーのみを記録
 - **error** - 特定のリクエストで致命的と判定されるエラー + fatalを記録
 - **warn** - 致命的でない問題の警告 + error + fatalを記録
 - **info** - アプリケーションの一般的な実行に関する情報 + warn + error + fatalを記録
 - **debug** - infoより詳細な情報 + info + warn + error + fatalを記録
 - **trace** - 非常に詳細な情報 + debug + info + warn + error + fatalを記録

デフォルトのレベルは `info` です。限られたフラッシュストレージの組込みデバイスではディスクへの書き込みを最小限にするために `fatal` を設定することもできます。

### Flowエディタの設定

adminAuth
: Flowエディタにログインフォームを設けます。詳細な情報は[認証](security)を参照してください。
  以下が設定例です。

      adminAuth: {
        type: "credentials",
        users: [{
          username: "admin",
          password: "$2a$08$zZWtXTja0fB1pzD4sHCMyOCMYz2Z6dNbM6tl8sJogENOMcxWV9DN.",
          permissions: "*"
        }]
      }

- `users`リストで複数のユーザを設定できます
- パスワードはbcryptアルゴリズムによるハッシュ値を指定します
- `permissions`でユーザ毎に`*`（フルアクセス）か`read`（read only）権限が付与できます

      node -e "console.log(require('bcryptjs').hashSync(process.argv[1], 8));" YOUR PASSWORD HERE

paletteCategories
: Flowエディタのパレットのカテゴリの順序を設定します。カテゴリがリストにない場合はパレットの最後に追加されます。デフォルトの順序は以下のとおりです。

      ['subflows', 'input', 'output', 'function', 'social', 'storage', 'analysis', 'advanced'],

   _Note_: Subflowを作成するまでSubflowカテゴリは空のままで
   パレットには表示されません。

### エディタテーマ

エディタのテーマは次の設定オブジェクトを使用して変更することができます。すべての項目はオプションです。

    editorTheme: {
        page: {
            title: "Node-RED",
            favicon: "/absolute/path/to/theme/icon",
            css: "/absolute/path/to/custom/css/file"
        },
        header: {
            title: "Node-RED",
            image: "/absolute/path/to/header/image", // or null to remove image
            url: "http://nodered.org" // optional url to make the header text/image a link to this url
        },
        deployButton: {
            type:"simple",
            label:"Save",
            icon: "/absolute/path/to/deploy/button/image" // or null to remove image
        },
        menu: { // Hide unwanted menu items by id. see editor/js/main.js:loadEditor for complete list
            "menu-item-import-library": false,
            "menu-item-export-library": false,
            "menu-item-keyboard-shortcuts": false,
            "menu-item-help": {
                label: "Alternative Help Link Text",
                url: "http://example.com"
            }
        },
        userMenu: false, // Hide the user-menu even if adminAuth is enabled
        login: {
            image: "/absolute/path/to/login/page/big/image" // a 256x256 image
        }
    },

### ダッシュボード

ui
: Node-RED-DashboardアドオンNodeのホームパスを指定できます。
これは**httpNodeRoot**の相対パスとなります。

    ui : { path: "mydashboard" },

### Nodeの設定

独自のNode Typeはファイルで提供される独自の設定を定義することができます。

functionGlobalContext
: Function Nodeの中では他のライブラリを`require`できませんが`functionGlobalContext`であらかじめ`require`しておくことで`context.global`からライブラリにアクセスできるようになります。以下が設定例。

      functionGlobalContext: { osModule:require('os') }

  上記のように設定してFunction Nodeで以下のようにしてDebug Nodeに吐くと`os`オブジェクトが格納されていることがわかります。ただしライブラリが`npm`でグローバルにインストールされているか、`settings.js`より下層にインストールされていなければいけません。

      var myos = global.get('osModule');

 <div class="doc-callout"><em>Note</em> : Node-RED v0.13以前のドキュメントでは、
 グローバルコンテキストにアクセスする方法は<code>context</code>のサブプロパティを参照する方法でした。:
 <pre>context.global.foo = "bar";
 var osModule = context.global.osModule;</pre>
 この方法はまだサポートされていますが、非推奨であり、<code>global.get</code>/<code>global.set</code>
 でのアクセスが推奨されます。これは将来のリリースでコンテキストデータが永続化できることを見越しています。
 </div>

debugMaxLength
: Debug NodeでサイドバーのDebugタブに表示される最大文字数を指定します。
  デフォルト: 1000

mqttReconnectTime
: MQTT Nodeの接続が切断された場合に再接続を試行するまでの待機時間（ミリ秒）。
  デフォルト: 5000

serialReconnectTime
: Serial Nodeの接続が切断された場合に再接続を試行するまでの待機時間（ミリ秒）。
  デフォルト: 5000ミリ秒（5秒）

socketReconnectTime
: TCP Nodeの接続が切断された場合に再接続を試行するまでの待機時間（ミリ秒）。
  デフォルト: 10000

socketTimeout
: TCP Nodeのタイムアウト時間（ミリ秒）。
  デフォルト: 120000
