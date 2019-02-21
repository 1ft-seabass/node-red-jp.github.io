---
layout: default
title: Dockerで実行する
---


Docker配下でNode-REDを実行する方法はたくさんあります。
このガイドではあなたができるいくつかの方法を紹介しています。

ここではDockerと[Docker Command Line](https://docs.docker.com/reference/commandline/cli/)についての
基礎的な知識があることを前提にしています。


### コンテナのバージョン

私たちは３つのタグバージョンでコンテナを[DockerHub](https://hub.docker.com/r/nodered/node-red-docker/)で公開しています。:

- `latest` - uses [official Node.JS v4 base image](https://hub.docker.com/_/node/).
- `slim` - uses [Alpine Linux base image](https://hub.docker.com/r/mhart/alpine-node/).
- `rpi` - uses [RPi-compatible base image](https://hub.docker.com/r/hypriot/rpi-node/).

Alpine Linuxを使用すると、ビルドされたイメージのサイズが縮小されます（約100MBから700MB）
ネイティブモジュールのコンパイルに必要な標準の依存関係が削除されます。
もしネイティブの依存関係を持つモジュールを追加する、標準イメージを使用する、または拡張する場合は、
標準のイメージもしくはslimイメージに不足したパッケージを追加して使用してください。

より新しいNode.js v8のベースイメージを利用した追加イメージは、以下のタグによって入手可能です。

- **v8** 
- **slim-v8**
- **rpi-v8**

Node-REDのリリースはバージョンラベルによってもタグ付けされており、特定のバージョンに変更することも可能です。: `latest:X.Y.Z`, 
`slim:X.Y.Z`, `rpi:X.Y.Z`.

### クイックスタート

`最新版`コンテナの実行:

    docker run -it -p 1880:1880 --name mynodered nodered/node-red-docker

*Note:* Raspberry Piでは`rpi`-tagged image: `nodered/node-red-docker:rpi`を利用します。

このコマンドはDockerHubから`nodered/node-red-docker`コンテナをダウンロードします。
`mynodered`という名前でインスタンス名の登録と 1880ポートを開放します。
端末にNode-RED startが表示されます。
起動したらブラウザで`http://{host-ip}:1880`へアクセスするとエディタが開きます。

`Ctrl-p` `Ctrl-q`を入力するとコンテナから離れます。
これによりバックグラウンドで実行されます。

コンテナへの再接続方法:

    docker attach mynodered

コンテナの停止方法:

    docker stop mynodered

コンテナの起動方法:

    docker start mynodered


<div class="doc-callout">
<p><em>Note</em> : あなたのフローはコンテナ上の<code>flows.json</code>に保存されます。
これは<code>FLOWS</code>環境変数によって
変更が可能です。:
</p>
<pre>docker run -it -p 1880:1880 -e FLOWS=my_flows.json nodered/node-red-docker</pre>
<p>Node.jsランタイム引数は、環境パラメータ（NODE_OPTIONS）を使用してコンテナに渡すことができます。 たとえば、Node.jsガベージコレクタで使用されるヒープサイズを修正するには、次のコマンドを使用します:
</p>
<pre>docker run -it -p 1880:1880 -e NODE_OPTIONS="--max_old_space_size=128" nodered/node-red-docker</pre>
</div>


### カスタマイズ方法

コンテナは、ディレクトリ`/data`をユーザ設定ディレクトリとして使用します。
ノードを追加するには、シェルをコンテナに開き、
適切な`npm install`コマンドを実行します:

    # コンテナのシェルを開きます。
    docker exec -it mynodered /bin/bash

    # コンテナに一旦入り、ノード上の/dataディレクトリでnpm installをします。
    cd /data
    npm install node-red-node-smooth
    exit

    # 新しいノードを読み込む為コンテナを再起動します。
    docker stop mynodered
    docker start mynodered

### コンテナの外部にデータを保管する方法

`/data`を外部ボリュームにマウントすることが可能です。:

    docker run -it -p 1880:1880 -v ~/node-red-data:/data --name mynodered nodered/node-red-docker

このコマンドはホストの`~/node-red-data`ディレクトリをコンテナのユーザ設定ディレクトリとしてマウントします。

コンテナに拡張ノードを追加するには、
`npm install`コマンドをホスト上で実行します。

    cd ~/node-red-data
    npm install node-red-node-smooth
    docker stop mynodered
    docker start mynodered

<div class="doc-callout">
<p><em>Note</em> : ネイティブの依存関係を持つモジュールはホストマシンのアーキテクチャ上でコンパイルされます。
これらのモジュールはアーキテクチャがコンテナのベースイメージと一致しない限り
Node-REDコンテナ内では機能しません。
ネイティブモジュールの場合は、
ローカルシェルを使用してインストールするか、package.jsonを更新して再ビルドすることをお勧めします。</p></div>


### ソースからコンテナをビルドする方法

コンテナをメンテナンスするためのDockerfilesは[ここの](https://github.com/node-red/node-red-docker), branchの下にあります。

独自のバージョンをビルドするには:

    git clone https://github.com/node-red/node-red-docker.git
    cd node-red-docker

    # タグを付けてビルドする
    docker build -f <version>/Dockerfile -t mynodered:<tag> .


### カスタムイメージのビルド方法

パブリックなNode-REDイメージをベースイメージとして新しいDockerイメージを作成すると、
ビルドプロセス中に余拡張ノードをインストールできます。

1. 内容を含む`Dockerfile`を作成します:

        FROM nodered/node-red-docker
        RUN npm install node-red-node-wordpos

2. 次のコマンドを実行してイメージをビルドします:

        docker build -t mynodered:<tag> .

これで`wordpos`ノードを含むNode-REDイメージを作成します。

### アップデート

ベースコンテナのアップデートは次のようにシンプルにできます。

    docker pull nodered/node-red-docker
    docker stop mynodered
    docker start mynodered

### コンテナのリンク

`--link`オプションを使用して
Dockerランタイム上のコンテナ間を内部的にリンクすることができます。

例えば、`mybroker`という名前のMQTTブローカコンテナを提供するコンテナがある場合は、
Node-REDコンテナを`link`パラメータで実行して２つを接続する事ができます:

    docker run -it -p 1880:1880 --name mynodered --link mybroker:broker nodered/node-red-docker

これにより`broker`はNode-REDコンテナ内の既知のホスト名となり、
フロー内のサービスにアクセスするために使用できます。
Dockerホストの外部に公開する必要はありません。

    [{"id":"190c0df7.e6f3f2","type":"mqtt-broker","broker":"broker","port":"1883","clientid":""},{"id":"37963300.c869cc","type":"mqtt in","name":"","topic":"test","broker":"190c0df7.e6f3f2","x":226,"y":244,"z":"f34f9922.0cb068","wires":[["802d92f9.7fd27"]]},{"id":"edad4162.1252c","type":"mqtt out","name":"","topic":"test","qos":"","retain":"","broker":"190c0df7.e6f3f2","x":453,"y":135,"z":"f34f9922.0cb068","wires":[]},{"id":"13d1cf31.ec2e31","type":"inject","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"x":226,"y":157,"z":"f34f9922.0cb068","wires":[["edad4162.1252c"]]},{"id":"802d92f9.7fd27","type":"debug","name":"","active":true,"console":"false","complete":"false","x":441,"y":261,"z":"f34f9922.0cb068","wires":[]}]
