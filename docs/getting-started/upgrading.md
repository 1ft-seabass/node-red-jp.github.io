---
layout: default
title: アップグレード
---

<div class="doc-callout"><em>Note</em>: Raspian JessieにプリインストールされているNode-REDをお使いの場合は<a href="/docs/hardware/raspberrypi#upgrading">アップグレード手順</a>を参照してください。</div>

現在、我々はnpmバージョン2を利用することをお勧めしています。アップグレードする前に `npm -v` コマンドを実行してインストールされているnpmのバージョンを確認してください。

    sudo npm i -g npm@2.x
    hash -r

グローバルnpmパッケージとしてのNode-REDをインストールしている場合は、次のコマンドを使用して最新のバージョンにアップグレードすることができます。

    sudo npm cache clean
    sudo npm update -g --unsafe-perm node-red

### Node.jsのアップグレード

Node.jsを `v0.10.x` から `v4.3.x` にアップグレードする場合は次のようにNode-REDを再インストールすることをお勧めします。

    sudo npm cache clean
    sudo npm install -g --unsafe-perm node-red

----

### Node-REDバージョン0.10.4以前からのアップグレード

バージョン0.10.4以前のNode-REDはデフォルトでインストールディレクトリにユーザデータを書き込んでいました。以降のバージョンではこの構造が変わりました。以下はユーザデータの移行方法です。

1. ユーザデータを保存する場所を選択します。Node-REDバージョン0.10.4以降ではデフォルトで `$HOME/.node-red` ですが、Node-RED起動時の `--userDir` オプションで任意の場所を指定できます。

2. 念のためNode-REDインストールディレクトリ全体のバックアップコピーを取ります。

3. 選択したユーザーデータディレクトリにNode-REDインストールディレクトリに存在する以下のファイルを移動します。

   - `settings.js` - カスタマイズしている場合
   - `flows_` で始まる全てのファイル
   - `.config.json`
   - `.sessions.json` - 存在する場合
   - `lib/` ディレクトリ全体
   - `nodes/` ディレクトリ全体（手動でインストールされている任意の追加ノード）

4. 移動先のディレクトリとファイルにNode-REDが書き込み権限を持っていることを確認します。

5. [インストール手順](installation)に従い新しいバージョンのNode-REDをインストールして古いバージョンのNode-REDを削除します。

6. 特定のnpmパッケージに依存しているNodeを手動でインストールしていた場合は、依存するnpmパッケージを[再インストール](adding-nodes)します。

<div class="doc-callout">
<em>Note</em>: node-gypは任意のネイティブライブラリを再コンパイルした場合に"nobody"ユーザが特定のディレクトリのアクセスに失敗したということです。この場合Nodeが正しくインストールされないことがあります（例えばserialport）`--unsafe-perm` オプションを使用することでNodeを正しくインストールできることがあります。
</div>
