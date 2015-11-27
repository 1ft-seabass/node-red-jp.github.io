---
layout: default
title: Installation
---

### Node.jsのインストール 

Node-REDはNode.js <code>0.10.x</code> と <code>0.12.x</code>をサポートしています。

<div class="doc-callout"><em>Note</em>:  Node <code>0.12.x</code> のサポートは、Node-RED <code>0.11.0</code>で追加されました。
我々のcore runtimeとnodeはテスト済みですが、サードパーティのノードに関してはサポートしていない可能性があります。</div>

<div class="doc-callout"><em>Note</em>: 我々は現在、node.js v4.x.x or versions of io.jsをサポートしていません。</div>

Node <code>0.12.x</code> の最新版は以下からダウンロードできます。:

 - Linux Binaries: [32-bit](https://nodejs.org/dist/v0.12.7/node-v0.12.7-linux-x86.tar.gz)
                   or
                   [64-bit](http://nodejs.org/dist/v0.12.7/node-v0.12.7-linux-x64.tar.gz)
 - Max OS X Installer: [Universal](https://nodejs.org/dist/v0.12.7/node-v0.12.7.pkg)
 - Windows Installer: [32-bit](https://nodejs.org/dist/v0.12.7/node-v0.12.7-x86.msi)
                      or
                      [64-bit](https://nodejs.org/dist/v0.12.7/x64/node-v0.12.7-x64.msi)

他のダウンロード元は [ここ](http://nodejs.org/dist/v0.12.7/)にあります。

OSのパッケージマネージャからインストールする場合は [ここ](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)をご参照ください。

ハードウェアへのインストール方法は、以下を参照してください。:

 - [Raspberry Pi](../hardware/raspberrypi.html)
 - [BeagleBone Black](../hardware/beagleboneblack.html)

### Node-REDをインストールする  

Node-RED 0.10.4 以降では, Node-REDをインストールする最も簡単な方法は、nodeのパッケージマネージャであるnpmを使用する方法です。 
グローバルインストールで、`node-red`コマンドをシステムに加えるには以下の命令を行ってください。:

    sudo npm install -g --unsafe-perm node-red

<div class="doc-callout">
<em>Note</em>: <code>sudo</code> はLinux/OS Xでルートユーザ以外のユーザとして実行するときに必要となるコマンドです。Windowsで実行するときは、<a href="https://technet.microsoft.com/en-gb/library/cc947813%28v=ws.10%29.aspx">Administratorとしてコマンドプロンプトから</a>,
<code>sudo</code>コマンドを使わずに実行してください。
</div>

<div class="doc-callout">
<em>Note</em>: インストール中、いくつかのエラーが<code>node-gyp</code>コマンドから報告される可能性があります。
それらのエラーは概して<em>致命的ではない</em> エラーであり、それらのエラーはオプショナルな依存性に関係しており、それらを構築するためのコンパイラを要求しています。
 <b>Node-RED はそれらのオプショナルな依存性にかかわらず動きます。</b>しかしnodeモジュールを追加する際にネイティブコードをコンパイルする必要がある場合があります。 <code>node-gyp</code>
のインストール方法は <a href="https://github.com/TooTallNate/node-gyp#installation">ここ</a>に記述されています。
</div>

### 他のインストール方法

#### zipをダウンロードする

最新版は[ここ](https://github.com/node-red/node-red/releases/latest)からダウンロードできます。
このzipファイルは`node-red-X.Y.Z` (`X.Y.Z`はバージョンナンバー) という最上位フォルダを持っています。
展開した後、最上位フォルダの中から、以下のコマンドを実行してください。:

    npm install --production

#### 開発者向け -GitHubを使う

GitHubからコードを実行するのは、開発コードの使用が好きな方か、コード開発に参加したい方にのみおすすめします。

GitHubからソースリポジトリをクローンするには以下のコマンドを実行します。:

    git clone https://github.com/node-red/node-red.git

クローンが終わったら、前もって必要なモジュールはインストールされているので以下のコマンドを実行します:

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

### 次のステップ

インストールが終わったら、[Node-REDの実行](running.html)を行います。
