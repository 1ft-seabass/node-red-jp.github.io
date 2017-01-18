---
layout: default
title: Running under Docker
---


Docker配下でNode-REDを実行する方法はたくさんあります。
このガイドではあなたがそれを行う方法を紹介しています。

ここではDockerと[Docker Command Line](https://docs.docker.com/reference/commandline/cli/)についての基礎的な知識があることを前提にしています。


### コンテナのバージョン

私たちは３つのタグバージョンでコンテナを
[DockerHub](https://hub.docker.com/r/nodered/node-red-docker/)で公開しています

- `latest` - uses [official Node.JS v4 base image](https://hub.docker.com/_/node/).
- `slim` - uses [Alpine Linux base image](https://hub.docker.com/r/mhart/alpine-node/).
- `rpi` - uses [RPi-compatible base image](https://hub.docker.com/r/hypriot/rpi-node/).

Alpine Linuxを使用すると、ビルドされたイメージのサイズが縮小されます（約100MBから700MB）
ネイティブモジュールのコンパイルに必要な標準の依存関係が削除されます。
もしネイティブの依存関係を持つモジュールを追加する、標準イメージを使用する、または拡張する場合は、
標準のイメージもしくはslimイメージに不足したパッケージを追加して使用してください。


### クイックスタート

`最新版`コンテナの実行:

    docker run -it -p 1880:1880 --name mynodered nodered/node-red-docker

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
<p><em>Note</em>: あなたのフローはコンテナ上の<code>flows.json</code>に保存されます。これは<code>FLOWS</code>環境変数によって変更が可能です:
</p>
<pre>docker run -it -p 1880:1880 -e FLOWS=my_flows.json nodered/node-red-docker</pre>
<p>Node.jsランタイム引数は、環境パラメータ（NODE_OPTIONS）を使用してコンテナに渡すことができます。 たとえば、Node.jsガベージコレクタで使用されるヒープサイズを修正するには、次のコマンドを使用します:
</p>
<pre>docker run -it -p 1880:1880 -e NODE_OPTIONS="--max_old_space_size=128" nodered/node-red-docker</pre>
</div>


### カスタマイズ方法

コンテナは、ディレクトリ`/data`をユーザ設定ディレクトリとして使用します。 ノードを追加するには、シェルをコンテナに開き、適切な`npm install`コマンドを実行します:

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

It is possible to mount the `/data` path on an external volume:

    docker run -it -p 1880:1880 -v ~/node-red-data:/data --name mynodered nodered/node-red-docker

This command mounts the host's `~/node-red-data` directory as the user configuration directory inside the container.

Adding extra nodes to the container can then be accomplished by running `npm install`
on the host machine:

    cd ~/node-red-data
    npm install node-red-node-smooth
    docker stop mynodered
    docker start mynodered

<div class="doc-callout">
<p><em>Note</em>: Modules with a native dependencies will be compiled on the host
machine's architecture. These modules will not work inside the Node-RED
container unless the architecture matches the container's base image. For native
modules, it is recommended to install using a local shell or update the
package.json and re-build.</p></div>


### ソースからコンテナをビルドする方法

The Dockerfiles for these containers are maintained [here](https://github.com/node-red/node-red-docker), each under its own branch.

To build your own version:

    git clone https://github.com/node-red/node-red-docker.git
    cd node-red-docker

    # Build it with the desired tag
    docker build -f <version>/Dockerfile -t mynodered:<tag> .


### カスタムイメージのビルド方法

Creating a new Docker image, using the public Node-RED images as the base image,
allows you to install extra nodes during the build process.

1. Create a file called `Dockerfile` with the content:

        FROM nodered/node-red-docker
        RUN npm install node-red-node-wordpos

2. Run the following command to build the image:

        docker build -t mynodered:<tag> .

That will create a Node-RED image that includes the `wordpos` nodes.

### アップデート

Updating the base container image is as simple as

    docker pull nodered/node-red-docker
    docker stop mynodered
    docker start mynodered

### コンテナのリンク

You can link containers "internally" within the Docker runtime by using the
`--link` option.

For example, if you have a container that provides an MQTT broker container called `mybroker`, you can run the Node-RED container with the `link` parameter to join the
two:

    docker run -it -p 1880:1880 --name mynodered --link mybroker:broker nodered/node-red-docker

This will make `broker` a known hostname within the Node-RED container that can be
used to access the service within a flow, without having to expose it outside
of the Docker host.

    [{"id":"190c0df7.e6f3f2","type":"mqtt-broker","broker":"broker","port":"1883","clientid":""},{"id":"37963300.c869cc","type":"mqtt in","name":"","topic":"test","broker":"190c0df7.e6f3f2","x":226,"y":244,"z":"f34f9922.0cb068","wires":[["802d92f9.7fd27"]]},{"id":"edad4162.1252c","type":"mqtt out","name":"","topic":"test","qos":"","retain":"","broker":"190c0df7.e6f3f2","x":453,"y":135,"z":"f34f9922.0cb068","wires":[]},{"id":"13d1cf31.ec2e31","type":"inject","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"x":226,"y":157,"z":"f34f9922.0cb068","wires":[["edad4162.1252c"]]},{"id":"802d92f9.7fd27","type":"debug","name":"","active":true,"console":"false","complete":"false","x":441,"y":261,"z":"f34f9922.0cb068","wires":[]}]
