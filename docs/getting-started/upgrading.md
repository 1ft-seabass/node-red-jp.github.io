---
layout: docs
toc: getting-started-toc.html
title: アップグレード
---

<div class="doc-callout"><em>Note</em> : Raspberry PiにプリインストールされているNode-REDをお使いの場合、
<a href="/docs/hardware/raspberrypi"><b>アップグレードガイド</b></a>に従うことを推奨します。</div>

グローバルnpmパッケージとしてインストールしたNode-REDを利用している場合、
以下のコマンドによって最新版にアップグレードすることができます。:

    sudo npm install -g --unsafe-perm node-red

ユーザディレクトリにインストールされている古いノードを確認したり、再インストールするためには2つの選択肢があります。:

Node-REDエディタのUIから`Manage Pallete`メニューを利用します。ノードの削除、追加、更新をおこなうことができます。または、以下のようにコマンドラインを利用します。;

    cd ~/.node-red
    npm outdated

これは更新可能なノードの一覧を表示します。
最新版のノードを再インストールする場合は以下のように実行します。:

    npm install foo          # to re-install the latest version of a node called foo

Node-REDを一旦停止し、再起動する必要があります。

## Node.jsのアップグレード

Node.jsをアップグレードした場合、例えばv4.xからv8.x、Node-REDを停止し、
以下のように再インストールしたほうがよいでしょう。:

    sudo npm cache clean --force
    sudo npm install -g --unsafe-perm node-red

依存関係を持つノードを再ビルドする必要もあります。
推奨されている`~/.node-red`にそれらをインストールした場合、このようになります。:

    cd ~/.node-red
    npm rebuild

その後、Node-REDを再起動します。

----

_Note_: `--unsafe-perm`オプションを利用する理由は、
node-gypがネイティブライブラリを再コンパイルしようとしたとき、"nobody"ユーザとして実行しようとし、
特定のディレクトリへのアクセスに失敗するためです。
これによって問題のあったノード（例 Serialport）はインストールされません。
インストール中にルートアクセスを許可することで、アップグレードにおいてノードを正常にインストールできます。
