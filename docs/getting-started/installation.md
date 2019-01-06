---
layout: default
title: インストール
---

Node-REDをインストール前に、Node.jsをインストールする必要があります。Node.js **LTS 8.x**を使用することをお勧めします。Node.js 6.xおよび4.xのユーザーは、最新の更新があることを確認する必要があります。Node-REDはNode.js 0.12.xまたは0.10.xをサポートしなくなりました。

特定のハードウェアプラットフォームおよびオペレーティングシステムインストール方法は、以下を参照してください。

 - [Raspberry Pi](../hardware/raspberrypi)
 - [BeagleBone Black](../hardware/beagleboneblack)
 - [Windows](../platforms/windows)

LinuxおよびOSXユーザーは、オペレーティングシステム用の**[パッケージ化された Node.js バージョン](https://nodejs.org/en/download/package-manager/)** をインストールするか、
最新のLong Term Support（LTS）バージョンを[ダウンロードサイト](https://nodejs.org/en/download/) から入手する必要があります。

Node.jsのバージョンを確認するには

    node -v

その他のダウンロード元は [こちら](https://nodejs.org/dist/latest-v8.x/) にあります。


最も簡単なNode-REDのインストール方法は、Node.jsに付随しているnode package manager（npm）を利用する方法です。
グローバルモジュールとしてインストールし、システムパスに`node-red`のコマンドを追加するには以下のようにします。:

    sudo npm install -g --unsafe-perm node-red

<div class="doc-callout">
Note: <code>sudo</code>はLinuxまたはOS Xの実行環境でのインストールでのみ必要です。
Windows環境であれば、<a href="../platforms/windows">Windows環境でのインストール手順</a>をご覧ください。
</div>

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
 <code>--production</code> オプションは使用しないのはこのためです。
</div>

また、アプリケーションをビルドしそれを使用する前に、`grunt-cli` をインストールする必要があります。
このインストールはグローバルで行う必要があります。

    sudo npm install -g grunt-cli

上のコマンドを実行した後、アプリケーションのビルドと実行が可能になります。

    grunt build
    node red

#### 次のステップ

インストールが終わったら、[Node-REDの実行](running)を行います。
