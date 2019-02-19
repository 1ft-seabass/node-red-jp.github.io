---
layout: default
title: BeagleBone基板で実行する
---

<a href="https://beagleboard.org/latest-images" target="bbb">beagleboard.org</a>から、
最新のSDカードイメージのDebian 9 Stretchを利用することを推奨します。

4GBイメージのBeagleBone基板はNode-REDがプリインストールされていて自動起動が設定されているため、
BeagleBoneを起動してブラウザでポート番号1880を表示することができます。

古いeMMCバージョンのBBBへのフラッシュに適した2GBコンソールバージョンは推奨されませんが、
以下の手動インストール手順にしたがってインストールすることができます。

Node-REDのログを確認するには

    sudo journalctl -f -u node-red -o cat

Node-REDを停止するには

    sudo service node-red stop

Node-REDを起動するには

    sudo service node-red start

起動のたびにNode-REDを自動起動するように設定するには

    sudo systemctl enable node-red.service

同様に起動時の自動起動を停止するには

    sudo systemctl disable node-red.service


---

#### アップグレード

最新のDebianイメージはNode-REDおよびNode.jsが既にインストールされています。 - 最も簡単なアップグレード方法はビルトインアップグレードツールを利用することです。:

    sudo apt update
    sudo apt upgrade nodejs bb-node-red-installer

また、Node-REDサービスを再起動する必要があり、開いているブラウザセッションをリフレッシュする必要があるでしょう。

2017 Debian 9.2版を利用している場合、まず`sudo apt full-upgrade`を実行する必要があるでしょう。

**Note**: Raspberry Pi / Debianアップグレードスクリプト（`update-nodejs-and-nodered`）を使わないでください。
異なる場所にNode.jsおよびNode-REDの両方を再インストールし、
競合が発生して既存のsystemd設定ファイルを破壊してしまうためです。

---

#### 設定

デフォルトではBeagleboneはrootとしてNode-REDを起動するように設定されています。
そして設定ファイルは`/root/.node-red`ディレクトリに位置し、これらを編集するにはroot権限（sudo）が必要です。
例えば`settings.js`ファイルを編集するには、ここで設定をおこないます。

また、Beagleboneはsystemdサービス、`/lib/systemd/system/node-red.socket`を持っており、接続しようとしたときに自動的にNode-REDを起動します。
デフォルトではこれはポート番号1880です。 - しかし、これを変更する必要がある場合、
`settings.js`を同様に変更する必要があります。

---

### マニュアルインストール

<div class="doc-callout">
このセクションはNode-REDを組み込まれたイメージを利用することによって非推奨となりました。上述の項目を参照してください。
</div>

#### 起動する前に

Debianの2GB eMMCバージョンを利用している場合、
これはすぐに削除されているため、まずユーティリティ関数をインストールする必要があるかもしれません。

    sudo apt-get update
    sudo apt-get install -y curl locales ntpdate avahi-utils python build-essential

ローカルタイムが正しく設定されていることを確認してください。
Beaglebone Blackにはバッテリーでバックアップされたリアルタイム・クロックが存在しないため、
ソフトウェア証明書の日付確認を有効にするために起動するたびに設定する必要があります。

    ntpdate -b -s -u pool.ntp.org

#### Node.jsをアップグレードする

Node.js LTS 8.xまたは10.xを利用することを推奨します。

    sudo apt-get install curl
    curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
    sudo apt-get install -y build-essential nodejs
    hash -r

#### Node-REDをインストールする

Node-REDをインストールする最も簡単な方法はnode's package manager、npmを利用することです。:

    sudo npm i -g --unsafe-perm node-red

_Note_: `--unsafe-perm`オプションを利用する理由は、node-gypがネイティブライブラリを再コンパイルする際に、
「nobody」ユーザとして実行しようとし、特定のディレクトリへのアクセスに失敗することが多いためです。
これはエラーのように見える警告を発生させますが、エラーであるのはときどきです。
このフラグを利用してrootとして実行することをnode-gypに許可することで、
この状況を回避し、代わりに本当のエラーを表示させます。

他のインストール方法、例えばGitHubから開発版を実行するには、[インストール手順](../getting-started/installation#install-node-red)を参照してください。

#### Beaglebone固有のノード

最も簡潔な方法でI / Oピンに直接アクセスできるようにするBeaglebone固有のノードがいくつかあります。
それらをインストールする最も簡単な方法はnpmから直接おこなうことです。

以下のコマンドを手動で実行してインストールします。:

    sudo npm install -g --unsafe-perm beaglebone-io johnny-five node-red-contrib-gpio

*Note*: BBB固有ノードセットも利用できます。
しかし、これらは既にサポートされていないライブラリ（octalbonescript）であり、将来的に利用することは推奨できませんが、
一般的なGPIOノード以上の追加機能を持っているために注目に値します。

    sudo npm install -g --unsafe-perm node-red-node-beaglebone

### Node-REDを起動する

Beaglebone基板では限られたメモリしか利用できないため、
Node-REDを以下のコマンドで起動することが望ましいです。

    node-red-pi

#### 自動起動

起動時にNode-REDを自動起動する最も簡単な方法は、ビルトインsystemdを利用することです。
これをおこなうには、以下の内容を含む`/lib/systemd/system/nodered.service`ファイルを作成します。

    # systemd service file to start Node-RED
    [Unit]
    Description=Node-RED graphical event wiring tool.
    Wants=network.target
    Documentation=http://nodered.org/docs/hardware/raspberrypi.html
    [Service]
    Type=simple
    # Run as root user in order to have access to gpio pins
    User=root
    Group=root
    Nice=5
    Environment="NODE_OPTIONS=--max-old-space-size=128"
    #Environment="NODE_RED_OPTIONS=-v"
    ExecStart=/usr/bin/env node-red-pi $NODE_OPTIONS $NODE_RED_OPTIONS
    KillSignal=SIGINT
    Restart=on-failure
    SyslogIdentifier=Node-RED
    [Install]
    WantedBy=multi-user.target

systemd設定をリロードし、サービスを有効にします。

    sudo systemctl daemon-reload
    sudo systemctl enable nodered.service

systemdはログのために`/var/log/system.log`を利用します。このログをフィルタリングするためには、以下を利用します。

    sudo journalctl -f -u nodered -o cat

### エディタを利用する

Node-REDを起動したら、ホスト名を変更していないと仮定して、
ブラウザで[http://beaglebone.local:1880](http://beaglebone.local:1880)を表示してください。
