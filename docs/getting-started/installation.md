---
layout: default
title: インストール
---

### Node.jsのインストール

Node-REDは、Node.js LTS <code>8.x</code>を使用することをお勧めします。Node.js 6.xおよび4.xのユーザーは、最新の更新があることを確認する必要があります。Node-REDはNode.js 0.12.xまたは0.10.xをサポートしなくなりました。

特定のハードウェアプラットフォームおよびオペレーティングシステムインストール方法は、以下を参照してください。

 - [Raspberry Pi](../hardware/raspberrypi.html)
 - [BeagleBone Black](../hardware/beagleboneblack.html)
 - [Windows](https://nodered.org/docs/platforms/windows) → 英語サイトに遷移します

LinuxおよびOSXユーザーは、オペレーティングシステム用の [パッケージ化された Node.js バージョン](https://nodejs.org/en/download/package-manager/) をインストールするか、ダウンロードサイトから [最新のLong Term Support（LTS）バージョン](https://nodejs.org/en/download/) を入手する必要があります。

Node.jsのバージョンを確認するには

    node -v

その他のダウンロード元は [こちら](https://nodejs.org/dist/latest-v8.x/) にあります。

### Node-REDをインストールする

Node-REDをインストールする最も簡単な方法は、nodeのパッケージマネージャであるnpmを使用する方法です。
グローバルインストールで`node-red`コマンドをシステムに加えるには以下の命令を行ってください。

    sudo npm install -g --unsafe-perm node-red

<div class="doc-callout">
<em>Note</em>: Node-REDのインストールに<code>npm 1.1</code>を使用しないでください。次のコマンドで最新の<code>npm 2.x</code>にバージョンアップしてください: <code>sudo npm install -g npm@2.x</code><br />
<em>Note</em>: <code>sudo</code> はLinux/OS Xでルートユーザ以外のユーザとして実行するときに必要となるコマンドです。Windowsで実行するときは、<a href="https://technet.microsoft.com/en-gb/library/cc947813%28v=ws.10%29.aspx">Administratorとしてコマンドプロンプトから</a>,
<code>sudo</code>コマンドを使わずに実行してください。<br />
<em>Note</em>: インストール中、いくつかのエラーが<code>node-gyp</code>コマンドから報告される可能性があります。
それらのエラーは概して<em>致命的ではない</em> エラーであり、それらのエラーはオプショナルな依存性に関係しており、それらを構築するためのコンパイラを要求しています。
 <b>Node-RED はそれらのオプショナルな依存性にかかわらず動きます。</b>しかしnodeモジュールを追加する際にネイティブコードをコンパイルする必要がある場合があります。 <code>node-gyp</code>
のインストール方法は <a href="https://github.com/TooTallNate/node-gyp#installation">ここ</a>に記述されています。
</div>

#### 次のステップ

インストールが終わったら[Node-REDの実行](running.html)を行います。

### 他のインストール方法

#### zipをダウンロードする

最新版は[ここ](https://github.com/node-red/node-red/releases/latest)からダウンロードできます。
このzipファイルは`node-red-X.Y.Z` (`X.Y.Z`はバージョンナンバー) という最上位フォルダを持っています。
展開した後、最上位フォルダの中から、以下のコマンドを実行してください。

    npm install --production

#### 開発者向け -GitHubを使う

GitHubからコードを実行するのは、開発コードの使用が好きな方か、コード開発に参加したい方にのみおすすめします。

GitHubからソースリポジトリをクローンするには以下のコマンドを実行します。

    git clone https://github.com/node-red/node-red.git

クローンが終わったら、前もって必要なモジュールはインストールされているので以下のコマンドを実行します。

    cd node-red
    npm install

<div class="doc-callout">
<em>Note</em>: gitのクローンから実行する際、全ての依存性をインストールする必要性があります。
 <code>--production</code> オプションは使用しないでください。
</div>

また、アプリケーションをビルドしそれを使用する前に、`grunt-cli` をインストールする必要があります。 このインストールはグローバルで行う必要があります。

    sudo npm install -g grunt-cli

上のコマンドを実行した後、アプリケーションのビルドと実行が可能になります。

    grunt build
    node red

#### 次のステップ

インストールが終わったら[Node-REDの実行](running.html)を行います。
