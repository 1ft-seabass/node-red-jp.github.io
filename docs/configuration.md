---
layout: default
title: 設定
---
Node-REDを設定するために以下のプロパティが利用できます。

スタンドアローンのアプリケーションとして実行すると、これらのプロパティは`settings.js`ファイルから読み込まれます。このファイルの場所は以下の順に決定されます。

 - 起動時に`--settings|-s`オプションで指定した場所
 - 起動時に`--userDir|-u`オプションで指定されたディレクトリ
 - デフォルトのユーザディレクトリ`$HOME/.node-red/settings.js`
 - Node-REDがインストールされたディレクトリ

Node-REDにはユーザが指定する`settings.js`が存在しない場合に使用するデフォルトの`settings.js`ファイルが含まれています。また、独自の`settings.js`の雛形として使用できます。[here](https://github.com/node-red/node-red/blob/master/settings.js)のGitHub上でも確認できます。

[既存アプリケーションへの組込み](embedding.html)の場合、`settings`プロパティは`RED.init()`の呼び出しでNode-REDへ渡されます。ただ、このモードで実行すると特定のプロパティは無視されます。

### Node-RED実行時の設定

flowFile
: Flow設定をファイルに保存する場合のファイル名です。デフォルト: `flows_<hostname>.json`

userDir
: Flow設定をファイルに保存する場合のファイルへのPathです。デフォルト: `$HOME/.node-red`

nodesDir
: 自作のNodeなどを追加するディレクトリを指定できます。ここで指定したディレクトリ内にNodeの`.js`と`.html`ファイルを配置するとNode-REDのパレットに表示されるようになります。

uiHost
: ホストです。デフォルト: `0.0.0.0` -
  *all IPv4 interfaces*.

  *Standalone only*.

uiPort
: ポートです。デフォルト: `1880`.

  *Standalone only*.

httpAdminRoot
: Flow EditorのPathを指定できます。ちなみに`false`を指定するとEditorが無効になります。デフォルト: `/`

httpAdminAuth
: *Deprecated*: see `adminAuth`.

  Flow EditorにBasic認証を設けます。以下が設定例。

      httpAdminAuth: {user:"nol", pass:"5f4dcc3b5aa765d61d8327deb882cf99"}

  `pass`はMD5ハッシュを指定します。上記の例では実際にBasic認証ダイアログへ入力するパスワードは`password`という文字列になるということです。コンソールなどで以下のnodeコマンドを実行することでハッシュ値を得られます。

      node -e "console.log(require('crypto').createHash('md5').update('YOUR PASSWORD HERE','utf8').digest('hex'))"

  *Standalone only*.

httpNodeRoot
: HTTP In NodeのHTTPエンドポイントのPathを指定できます。ちなみに`false`Editorが無効になります。デフォルト: `/`

httpNodeAuth
: HTTP In NodeのHTTPエンドポイントにBasic認証を設けます。設定方法は`httpAdminAuth`と同様です。

httpRoot
: `httpRoot`を指定すると`httpAdminRoot`と`httpNodeRoot`を同じPathで上書きします。

https
: HTTPSを有効にします。以下が設定例。詳細は[here](http://nodejs.org/api/https.html#https_https_createserver_options_requestlistener)。

      https: {
        key: fs.readFileSync('privatekey.pem'),
        cert: fs.readFileSync('certificate.pem')
      },

  *Standalone only*.

disableEditor
: `true`を指定するとFlow Editorが無効になります。`httpAdminRoot`を`false`にした場合はUIもAPIも無効になりますが、こちらはUIのみ無効になります。デフォルト: `false`

httpStatic
: ここで指定したPathを静的なWebコンテンツとして表示できます。例えば`/home/username/.node-red/`と指定した場合、`/home/username/.node-red/index.html`を作成すると`/`へアクセスすると作成した`index.html`が表示されます。したがって`httpStatic`を指定する場合は`httpAdminRoot`は`/`より下層のPathにする必要があります。

  *Standalone only*.

httpStaticAuth
: `httpStatic`にBasic認証を設けます。設定方法は`httpAdminAuth`と同様です。

httpNodeCors
: HTTP In NodeのHTTPエンドポイントのCross-Origin Resource Sharing (CORS) を有効にします。以下が設定例。詳細は[here](https://github.com/troygoode/node-cors#configuration-options)

      httpNodeCors: {
        origin: "*",
        methods: "GET,PUT,POST,DELETE"
      },

httpNodeMiddleware
: HTTP In NodeのHTTPエンドポイントにHTTPミドルウェアを追加できます。HTTPエンドポイントでセッションとクッキーのハンドラを利用したい場合は以下のように設定します。ミドルウェア機能の形式は[こちら](http://expressjs.com/guide/using-middleware.html#middleware.application)で文書化されています。

      httpNodeMiddleware: function(req,res,next) {
        session({
          store: new MongoStore({
            url: process.env.MONGOLAB_URI
          })
        })(req, res, function() {
          cookieParser()(req, res, next);
        });
      },

### Flow Editorの設定

adminAuth
: Flow Editorにログインフォームを設けます。以下が設定例。

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
: Flow Editorのパレットのカテゴリの順序を設定します。カテゴリがリストにない場合はパレットの最後に追加されます。デフォルトの順序は以下。

      ['subflows', 'input', 'output', 'function', 'social', 'storage', 'analysis', 'advanced'],

   _Note_: Sub Flowを作成するまでSub Flowカテゴリは空のままでパレットには表示されません。

### Nodeの設定

独自のNode Typeはファイルで提供される独自の設定を定義することができます。

functionGlobalContext
: Function Nodeの中では他のライブラリを`require`できませんが`functionGlobalContext`であらかじめ`require`しておくことで`context.global`からライブラリにアクセスできるようになります。以下が設定例。

      functionGlobalContext: { os:require('os') }

  上記のように設定してFunction Nodeで以下のようにしてDebug Nodeに吐くと`os`オブジェクトが格納されていることがわかります。ただしライブラリが`npm`でグローバルにインストールされているか、`settings.js`より下層にインストールされていなければいけません。

      msg.payload = context.global.os;
      return msg;

debugMaxLength
: Debug NodeでDebugに表示する文字数を指定します。Debugに表示する文字が`...`で途切れてしまう場合はここを増やします。デフォルト: 1000

mqttReconnectTime
: MQTT Nodeの接続が切断された場合の再接続までの待機時間。デフォルト: 5000ミリ秒（5秒）

serialReconnectTime
: Serial Nodeの接続が切断された場合の再接続までの待機時間。デフォルト: 5000ミリ秒（5秒）

socketReconnectTime
: TCP Nodeの接続が切断された場合の再接続までの待機時間。
  デフォルト: 10000ミリ秒（10秒）

socketTimeout
: TCP Nodeのタイムアウト時間。デフォルト: 120000ミリ秒（120秒）
