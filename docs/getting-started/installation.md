---
layout: docs
toc: getting-started-toc.html
title: インストール
---

Node-REDをインストールする前に、Node.jsをインストールする必要があります。Node.js **LTS 8.xまたは10.x**を使用することをお勧めします。Node-REDはNode.js 4.xまたはそれ以前のバージョンをサポートしなくなりました。

特定のハードウェアプラットフォームおよびオペレーティングシステムでのインストール方法は、以下を参照してください。

 - [Raspberry Pi](../hardware/raspberrypi)
 - [BeagleBone Black](../hardware/beagleboneblack)
 - [Windows](../platforms/windows)

LinuxおよびOSXユーザーは、自身のオペレーティングシステム用の**[パッケージ化されたNode.js](https://nodejs.org/en/download/package-manager/)** をインストールするか、
最新のLong Term Support（LTS）バージョンを[ダウンロードサイト](https://nodejs.org/en/download/)から入手する必要があります。

Node.jsのバージョンを確認するには

    node -v

その他のダウンロードオプションは[こちら](https://nodejs.org/dist/latest-v8.x/)にあります。


最も簡単なNode-REDのインストール方法は、Node.jsに付随しているnode package manager（npm）を利用する方法です。
グローバルモジュールとしてインストールし、システムパスに`node-red`のコマンドを追加するには以下のようにします。:

    sudo npm install -g --unsafe-perm node-red

<div class="doc-callout">
Note: <code>sudo</code>はLinuxまたはOS Xの実行環境でのインストールにおいてのみ必要です。
Windows環境であれば、<a href="../platforms/windows">Windows環境でのインストール手順</a>をご覧ください。
</div>

### Install via SNAP

OSが [SNAPs](https://snapcraft.io/docs/core/install) をサポートしていれば、次のようにNode-REDをインストールできます。

    snap install node-red

しかし、これはセキュアコンテナ内で実行されるため、
ネイティブプラグインを持つノードをコンパイルするための
gcc、git(プロジェクト用)、GPIOへの直接アクセス、その他の外部プログラムなど
あなたが必要とするかもしれない外部の資源にアクセスできないことを注意してください。
コンテナのセキュリティを下げた"classic"モードでの実行もできますが、
我々はそれを読者の練習のために残しています。

#### 次のステップ

インストールが終わったら[Node-REDの実行](running.html)を行います。

---

### 他のインストール方法

#### zipをダウンロードする

最新版は[こちら](https://github.com/node-red/node-red/releases/latest)からダウンロードできます。
このzipファイルは`node-red-X.Y.Z` (`X.Y.Z`はバージョンナンバー) という最上位フォルダを持っています。
展開した後、最上位フォルダの中から、
以下のコマンドを実行してください。:

    npm install --production

#### 開発者向け -GitHubを使う

GitHubからコードを実行するのは、
開発コードの使用が好きな方か、コード開発に参加したい方にのみおすすめします。

GitHubからソースリポジトリをクローンするには以下のコマンドを実行します。

    git clone https://github.com/node-red/node-red.git

クローンが終わったら、前もって必要なモジュールはインストールされているので以下のコマンドを実行します。

    cd node-red
    npm install

<div class="doc-callout">
<em>Note</em> : gitのクローンから実行する際、
productionレベルの依存性だけでなく、全ての依存性をインストールする必要性があります。
 <code>--production</code>オプションを使用しないのはこのためです。
</div>

また、アプリケーションをビルドし、それを使用する前に`grunt-cli` をインストールする必要があります。
このインストールはグローバルで行う必要があります。

    sudo npm install -g grunt-cli

上のコマンドを実行した後、アプリケーションのビルドと実行が可能になります。

    grunt build
    npm start

#### 次のステップ

インストールが終わったら、[Node-REDの実行](running)を行います。
