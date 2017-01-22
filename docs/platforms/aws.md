---
layout: default
title: Running on Amazon Web Services
---

このガイドではAWS EC2インスタンスでNode-REDを実行する手順を紹介します。

#### ベースのEC2イメージを作成する

1. [AWS EC2 console](https://console.aws.amazon.com/ec2)にログインします。

2. 'インスタンスの作成'をクリックします。

3. クイックスタートリストから **Ubuntu Server** を選びます。

4. インスタンスタイプを選びます。`t2.micro`が始めるにはちょうど良いでしょう。

5. 'セキュリティグループの設定' タブの'ルールの追加'をクリックし、'カスタムTCPルール' ポート 1880を指定、送信元 'カスタム' 0.0.0.0/0を指定します。

6. '確認' タブの'作成' ボタンをクリックします。

7. SSHキーの設定を求めるメッセージが表示されます。'新しいキーペアの作成' とキーペア名を指定し、'キーペアのダウンロード'をクリックします。 ブラウザに`.pem`ファイルが保存されます。ファイルを安全な場所に保管し、最後に'インスタンスの作成'をクリックします。

数分後、EC2インスタンスが起動します。
コンソールからインスタンスのIPアドレスを見つけてください。

#### Node-REDのセットアップ

次はインスタンスにログインし、node.js と Node-REDをインストールします。

AWSガイド[Linuxインスタンスへの接続](http://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/AccessingInstances.html) に従って接続します。

ログインしたら、node.js と Node-REDをインストールする必要があります

       curl -sL https://deb.nodesource.com/setup_4.x | sudo -E bash -
       sudo apt-get install -y nodejs build-essential
       sudo npm install -g node-red


この時点で、`node-red`インスタンスをテストすることができます。*Note*: シリアルノードに関するいくつかのエラーが発生する可能性があります。これは予期されるもので、無視することができます。

起動したら、`http://<your-instance-ip>:1880/`のエディタにアクセスできます。

インスタンスが再起動されるたびにNode-REDが自動的に起動するようにするにはpm2を使用できます:

       sudo npm install -g pm2
       pm2 start `which node-red` -- -v
       pm2 save
       pm2 startup

*Note:* この最後のコマンドでさらにコマンドを実行するように促されるので、従って実行して下さい。

#### 次のステップ

このガイドでは、EC2で実行するようにインスタンスを構成する方法についてはほとんど触れていません。
Node-REDは、HTTPサーバを公開する単なるnode.jsアプリケーションです。
-他に何が可能化を学ぶために使用できる多くのオンラインガイドがあります。
