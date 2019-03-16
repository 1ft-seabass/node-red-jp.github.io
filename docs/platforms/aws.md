---
layout: default
title: Amazon Web Servicesで実行する
---

このガイドではAWS環境においてNode-REDを実行する手順を紹介します。

以下の2つの方法があります。

1. [AWS Elastic Beanstalk Service (EBS)で実行](#aws-ebsで実行)
2. [AWS EC2のUbuntuイメージ環境で実行](#aws-ec2のubuntuイメージ環境で実行)

## AWS EBSで実行

#### 前提条件

1. Elastic Beanstalk、SQSおよびS3を利用できるAWSアカウントを保有していることを確認します。

2. EBコマンドラインをダウンロードし、ローカルPCにインストールします。[リンク先](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/GettingStarted.html) を参照してください。

3. AWS認証情報を作成し、以下のようにローカルファイル(~/.aws/config or Usersusername.awsconfig)として保存してください。

    ```
[profile eb-cli]
aws_access_key_id = key id
aws_secret_access_key = access key
```

#### EB環境を作成する

1. ディレクトリを作成します (例. `demoapp`)。

2. 作成したディレクトリに移動します。

3. `exb init`を実行して新規のelastic beanstalkプロジェクトを作成します。好みのリージョンを選択し、プラットフォームにNode.jsを利用します。
SSHを利用したい場合、新規のキーペアを生成するのであればコンピュータにSSHがインストールされていることを確認してください。

4. ブラウザでAWSコンソールにログインし、Identity and Access Management（IAM）を選択し、AmazonS3FullAccessポリシーをaws-elasticbeanstalk-ec2-roleに追加します。Note: これによってEBSからS3へのフルアクセスが可能になります。このポリシー自分のセキュリティ要件に応じて調整することを推奨します。

#### Node-RED環境を構築する

1. `package.json`ファイルを以下の内容で作成します（"demoapp"は自分のアプリ名に置換してください）。

    ```javascript
{   
    "name": "demoapp",
    "version": "1.0.0",
    "description": "node-red demo app",
    "main": "",
    "scripts": {
        "start": "./node_modules/.bin/node-red -s ./settings.js"
    },
    "engines": {
        "node": "10.x"
    },
    "dependencies": {
        "node-red": "0.20.x",
        "aws-sdk": "2.4.x",
        "node-red-contrib-storage-s3": "0.0.x",
        "when": "3.7.x"
    },
    "author": "",
    "license": "ISC"
}
```

2. [Node-RED settings.js file](https://github.com/node-red/node-red/blob/master/settings.js)をdemoapp ディレクトリにコピーしてください。

3. settings.jsファイルを編集して、以下のエントリをmodule.exportsに追加します(awsRegionをeb initで使用したものに設定し、demoappは自分のアプリ名に置換してください) 。:

    ```
     awsRegion: 'eu-west-1',
     awsS3Appname: 'demoapp',
     storageModule: require('node-red-contrib-storage-s3'),
```

4. コマンドプロンプトで自分のアプリケーションのトップレベルディレクトリにいることを確認し、`eb create`コマンドを実行します; アプリケーション名をもっとユニークにしたいかもしれません。実行には長い時間がかかりますが、最終的には正常に完了します。

#### Node-REDのアクセス設定

この段階でのNode-REDはアプリケーションのURLから直接アクセスすることができます。しかし、これはセキュアではなく、ログも充分に出力されません。そこで、使用中のEC2インスタンスのNode-REDの管理ポートへの直接アクセスを設定します。

1. AWSコンソールでEC2を選択、セキュリティグループを選択します。セキュリティグループの一覧が表示されます。自分の環境の名称および"Security Group for ElasticBeanstalk Environment"と説明されているグループを選択します。選択したら、"Actions" そして"Edit inbound settings"をクリックします。ルールが記載されたダイアログボックスが表示されます。新規のルールを追加します。typeを"all traffic"に、sourceを"my ip"に設定し、ルールを保存します。

2. Node-REDアプリケーションが実行されているEC2インスタンスを選択します。IPアドレスをコピーします。

3. ポート番号8081でIPアドレスをブラウザに入力します。これでNode-REDの管理コンソールに直接アクセスできます。

Note: パブリックIPアドレスはNode-REDアプリケーションへのアクセスも可能にするため、同時にそのアクセス、つまりポート番号80でのアクセスをHTTPルールで削除することを推奨します。

Node-REDインスタンスがEBS上で実行できました。作成したフローはすべてAWS S3に保存されるため、環境を破壊することができ、フローは再デプロイするたびにアクセスできます。


## AWS EC2のUbuntuイメージ環境で実行

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

       curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
       sudo apt-get install -y nodejs build-essential
       sudo npm install -g node-red


この時点で、`node-red`インスタンスをテストすることができます。
*Note*: シリアルノードに関するいくつかのエラーが発生する可能性があります。
これは予期されるもので、無視することができます。

起動したら、`http://<your-instance-ip>:1880/`のエディタにアクセスできます。

インスタンスが再起動されるたびに
Node-REDが自動的に起動するようにするにはpm2を使用できます。:

       sudo npm install -g pm2
       pm2 start `which node-red` -- -v
       pm2 save
       pm2 startup

*Note:* この最後のコマンドでさらにコマンドを実行するように促されるので、従って実行して下さい。

#### 次のステップ

このガイドでは、EC2で実行するようにインスタンスを構成する方法については
ほとんど触れていません。
Node-REDは、HTTPサーバを公開する単なるnode.jsアプリケーションです。
他に何が可能なのかを学ぶために利用できる多くのオンラインガイドがあります。
