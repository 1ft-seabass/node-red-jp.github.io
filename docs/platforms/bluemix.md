---
layout: default
title: Running on IBM Bluemix
---

Node-REDは、IBM Bluemix上で、[ボイラーテンプレートプリケーション](#boilerplate-application)として提供されています。
また、私たちは数クリックで'[Bluemixへデプロイする機能](#deploy-to-bluemix)'も提供しています。

---

### ボイラーテンプレートアプリケーション

1. [Bluemix.net](http://bluemix.net)にて、アカウントをサインアップし、ログイン

2. カタログへ移動し、['Node-RED'というキーワードで検索](https://new-console.ng.bluemix.net/catalog/starters?search=Node-RED)。

3. ここで、以下の2つのボイラーテンプレートが表示されます:

    1. **Node-RED Starter** - 通常のNode-RED

    2. **Internet of Things Platform Starter** - Watson IoT Platform向けNode-RED(Watson IoT Platformのデフォルトのフローなどが用意されている)

   両方のボイラーテンプレートは、以下の2つが含まれています:

     - フローの定義を格納するためのCloudantデータベース
     - Watson IoT platformやWatson等のBluemix上のサービスにアクセスするためのノード

4. どちらかのボイラーテンプレートを選択し、アプリケーション名を入力後、アプリケーションを作成

数分後、`https://<yourAppName>.mybluemix.net`というURLから、Node-REDインスタンスへアクセスできます。

#### Node-REDをカスタマイズ

##### フローエディタにログイン認証を追加

ユーザ名とパスワードを環境変数へ登録することで、 設定ファイルを編集することなく、フローエディタへのアクセスを制限できます:

1. Bluemixダッシュボードにて、アプリケーションの環境変数のページに移動
2. 以下のユーザ定義変数を追加:
    - `NODE_RED_USERNAME` - フローエディタのログインに用いるユーザ名
    - `NODE_RED_PASSWORD` - フローエディタのログインに用いるパスワード
3. 保存をクリック

##### ノードの追加

ノードを追加するためには、Node-REDアプリケーションの`package.json`ファイルを編集します。
具体的には、`package.json`ファイルの`dependencies` セクションに必要なノードモジュールを追加します。
追加するフォーマットは、`"node-red-node-package-name":"x.x.x"`です。
フォーマット中のx.x.xには追加したいノードのバージョン番号を記載します。

##### 静的なウェブコンテンツを変更

静的なウェブコンテンツは`public`ディレクトリ内に保存します。

もし、ルートパスの静的なウェブコンテンツを削除し、ルートパスをフローエディタに変更したい場合は、
`bluemix-settings.js`ファイル内の`httpStatic`と`httpAdminRoot`エントリを削除します。

##### Node-REDを最新バージョンへ更新

ボイラーテンプレートは、常に最新の安定板Node-REDを取得するよう設定されています。
そのため、[`cf` command-line tool](https://console.ng.bluemix.net/docs/cli/reference/cfcommands/index.html)コマンドを用いて、アプリケーションをrestageすると、最新バージョンへ更新されます。

---

### Deploy To Bluemix

The [Deploy To Bluemix enabled repository](https://github.com/node-red/node-red-bluemix-starter)
lets you create your own customised Node-RED application that can then
be deployed to Bluemix with a couple clicks.

You can try it out now by clicking here:

[![Deploy to Bluemix](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/node-red/node-red-bluemix-starter.git)

When you click the button, you are taken to Bluemix where you get a pick a name
for your application at which point the platform takes over, grabs the code from
this repository and deploys it.

It will automatically create an instance of the Cloudant service, call it
`sample-node-red-cloudantNoSQLDB` and bind it to you application. This is where your
Node-RED instance will store its data. If you deploy multiple instances of
Node-RED from this repository, they will share the one Cloudant instance.

It includes a set of default flows that are automatically deployed the first time
Node-RED runs.

#### Customising your Node-RED repository

The repository is there to be cloned, modified and re-used to allow anyone to create
their own Node-RED based application that can be quickly deployed to Bluemix.

The default flows are stored in the `defaults` directory in the file called `flow.json`.

The web content you get when you go to the application's URL is stored under the
`public` directory.

Additional nodes can be added to the `package.json` file and all other Node-RED
configuration settings can be set in `bluemix-settings.js`.

If you do clone the repository, make sure you update the `README.md` file to point
the `Deploy to Bluemix` button at your repository.

If you want to change the name of the Cloudant instance that gets created, the memory
allocated to the application or other deploy-time options, have a look in `manifest.yml`.
