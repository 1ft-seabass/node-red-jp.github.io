---
layout: default
title: IBM Bluemixで実行する
---

Node-REDは、IBM Bluemix上で、
[ボイラーテンプレートプリケーション](#boilerplate-application)として提供されています。

また、私たちは数クリックで'[Bluemixへデプロイする機能](#deploy-to-bluemix)'も提供しています。

---

### ボイラーテンプレートアプリケーション

1. [Bluemix.net](http://bluemix.net)にて、アカウントをサインアップし、ログイン

2. カタログへ移動し、['Node-RED'というキーワードで検索](https://new-console.ng.bluemix.net/catalog/starters?search=Node-RED)。

3. ここで、以下の2つのボイラーテンプレートが表示されます:

    1. **Node-RED Starter** - 通常のNode-RED

    2. **Internet of Things Platform Starter** - Watson IoT Platform向けNode-RED
    　　(Watson IoT Platformでどのように動作するのかを
       示すデフォルトのフローなどが用意されている)

   両方のボイラーテンプレートは、以下の2つが含まれています:

     - フローの定義を格納するためのCloudantデータベース
     - Watson IoT platformやWatson等のBluemix上のサービスに
       アクセスするためのノード

4. どちらかのボイラーテンプレートを選択し、アプリケーション名を入力後、アプリケーションを作成

数分後、`https://<yourAppName>.mybluemix.net`というURLから、Node-REDインスタンスへアクセスできます。


#### Node-REDをカスタマイズ

Node-REDインスタンスをカスタマイズするため、アプリケーションをローカルにダウンロードするか、アプリケーションのIBM Cloud dashboardページからContinuous Delivery integrationオプションを有効化することができます。GitHubまたはIBM DevOpsサービスにGitリポジトリを生成し、そのリポジトリでNode-REDをカスタマイズ、変更の保存、IBMクラウドのアプリケーションの自動更新をおこなうことができます。

##### フローエディタにログイン認証を追加

NOde-REDインスタンスを初めて実行したとき、エディタを保護するためのいくつかのオプションが表示されます。
これらのオプションを変更するため、IBM Cloudコンソールまたはcfコマンドで環境変数を設定します。

1. Bluemixダッシュボードにて、アプリケーションの"環境変数"ページに移動
2. 以下のユーザ定義変数を追加:
    - `NODE_RED_USERNAME` - フローエディタのログインに用いるユーザ名
    - `NODE_RED_PASSWORD` - フローエディタのログインに用いるパスワード
    - `NODE_RED_GUEST_USER` - trueの場合、フローエディタに読み取り専用権限を持つ匿名ユーザを許可します
3. 保存をクリック

##### ノードの追加

エディタのドロップダウンメニューから`manage palette`オプションを選択することで、
ノードを追加することができます。

他には、Node-REDアプリケーションの`package.json`ファイルを編集します。
具体的には、`package.json`ファイルの`dependencies` セクションに必要なノードモジュールを追加します。
追加するフォーマットは、`"node-red-node-package-name":"x.x.x"`です。フォーマット中のx.x.xには追加したいノードのバージョン番号を記載します。

##### Node-REDを最新バージョンへ更新

アプリケーションのpackage.jsonはは、常に最新の安定板Node-REDを取得するよう設定されています。
新しいリリースが利用可能になった場合にアップグレードを実行させるには以下のようにします。:

1. デフォルトでは、ノード・アプリケーションごとに[IBM Cloudはキャッシュ・ディレクトリを保有し](https://console.bluemix.net/docs/runtimes/nodejs/configurationOptions.html#cache_behavior)し、既に解決された依存関係を毎回ダウンロードしてインストールすることがないように、保管しています。更新するにはこのキャッシュを無効にしなくていけません。環境変数NODE_MODULES_CACHEをfalseに設定します。この設定は、自分のアプリケーションのIMM Cloudコンソールページ（Runtime -> Environment Variables）またはcfコマンドラインツールで以下のように実行することで設定できます。:

        cf set-env [APPLICATION_NAME] NODE_MODULES_CACHE false

2. アプリケーションを再起動します。このときIBM Cloudコンソールは利用できないため、cfコマンドラインツールを使用しなくてはいけません。:

        cf restage [APPLICATION_NAME]

3. Node-RED 0.20以降にアップグレードする場合、アプリケーションがNode.js 8以降で動作していることを確認してください。そうするためには、アプリケーションの`package.json`ファイルを編集 - 以下のファイルの編集方法を参照してください。`engines`プロパティが設定されていない場合、`8.x`に更新します。

このファイルを編集するため、アプリケーションのIBM Cloud dashboardページから
Continuous Delivery integrationオプションを有効にする必要があります。
GitHubまたはIBM DevOpsサービスにGitリポジトリを生成し、
そのリポジトリでNode-REDをカスタマイズ、変更の保存、IBMクラウドのアプリケーションの自動更新をおこなうことができます。


##### 静的なウェブコンテンツを変更

インスタンスのランディングページは、アプリケーションの静的コンテンツから提供されます。
静的なウェブコンテンツは`public`ディレクトリ内に保存します。

もし、ルートパスの静的なウェブコンテンツを削除し、ルートパスをフローエディタに変更したい場合は、
`bluemix-settings.js`ファイル内の`httpStatic`と`httpAdminRoot`エントリを削除します。

---

### Bluemixへデプロイ

[Bluemixデプロイ向けリポジトリ](https://github.com/node-red/node-red-bluemix-starter)を用いると、
カスタマイズしたNode-REDアプリケーションを作成し、
数クリックでBluemixへデプロイできます。

以下をクリックすることで、すぐにデプロイを試すことができます:

[![Bluemixへデプロイ](https://bluemix.net/deploy/button.png)](https://bluemix.net/deploy?repository=https://github.com/node-red/node-red-bluemix-starter.git)

このボタンをクリックすると、
Bluemixへ移動し、引き継き先のアプリケーション名を入力できます。
その後、リポジトリからコードを取得し、デプロイされます。

この手順では、自動的に `sample-node-red-cloudantNoSQLDB` という名前のCloudantサービスのインスタンスが作成され、
アプリケーションとバインドされます。
Node-REDインスタンスは、Cloudantサービスへデータを保存します。
もし、このリポジトリから複数のNode-REDインスタンスをデプロイする場合、複数のNode-REDインスタンス間で1つのCloudantインスタンスを共有することもできます。

この手順では、Node-REDを初めて起動した際に、
デフォルトのフローが自動的にデプロイされます。

#### Node-REDリポジトリをカスタマイズ

Node-REDリポジトリは、すぐにBluemixへデプロイ可能な、
あなた自身のNode-REDベースのアプリケーションを誰でも作成できるよう、複製、修正、再利用ができます。

デフォルトのフローは、 `defaults` ディレクトリ内の `flow.json` ファイルに保存します。

アプリケーションのURLへアクセスした時に参照できる静的ウェブコンテンツは、
`public` ディレクトリ以下に保存します。

追加ノードがある場合は、 `package.json` ファイルに追加します。
その他の全てのNode-REDの設定は、`bluemix-settings.js` へ設定します。

もし、リポジトリをCloneした場合は、
`Bluemixへデプロイ` ボタンの参照先があなたがCloneしたリポジトリとなるよう `README.md` ファイルを更新してください。

もし、作成するCloudantインスタンス名、アプリケーションに割り当てるメモリ量、デプロイ時の他のオプションを変更したい場合は、
`manifest.yml` を参照し、変更してください。
