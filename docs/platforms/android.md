---
layout: default
title: Androidで実行する
---

アプリストアの<a href="https://termux.com">Termux</a>アプリを利用することで、簡単にAndoroidデバイス上でNode-REDを実行することができます。このアプリは<a href="https://play.google.com/store/apps/details?id=com.termux&amp;hl=en_GB">Playストア</a>から入手できます。

<div class="doc-callout"><em>Note</em> : Node-REDチームはTermuxアプリケーションおよびその開発に何の関わりも持っていません。現時点では動作していると言う以外にサポートを提供することはできません。</div>

アプリをインストールし、起動します。そしてプロンプトに以下のように入力します。

    apt update
    apt upgrade
    apt install coreutils nano nodejs
    npm i -g --unsafe-perm node-red
    node-red

そしてブラウザで`localhost:1880`を指定します。


#### 注意

 - 一般的な方法によって、`node-red-dashboard`のようなNode-REDの他のノードをnpm installすることもできます。:

    cd ~/.node-red
    npm i node-red-dashboard

 - `volume-down`キーはctrlキーです。 - そのため、`vol-down-c`によって実行中のアプリを「終了」することができます。
 - 上述の手順で`nano`エディタもインストールされ、これはファイルの編集に役立ちます。


### その他

#### 自動起動

Termux内のアプリの自動起動についてのディスカッションについては、Termux GitHubプロジェクトの<a href="https://github.com/termux/termux-app/issues/14">このスレッド</a>を参照してください。

起動時のTermux自動起動に役立つアプリがあります。 - <a href="https://play.google.com/store/apps/details?id=com.autostart&amp;hl=en_GB">Autostart - No Root</a>

#### デバイスアクセス

追加のTermuxデバイスプラグインを利用することで様々なハードウェアに直接アクセスすることもできます。 - 勿論、これはexecコマンドを利用して`Node-RED`を介してアクセスすることもできます。

**Note**: TermuxにアドオンアプリとアドオンAPIの両方をインストールする必要があります。

Playストアからアドオンアプリ - <a href="https://play.google.com/store/apps/details?id=com.termux.api&amp;hl=en">Termux:API</a>をインストールします。

Termuxにアドオンアクセスをインストールします。

    apt install termux-api

リンク - <a href="https://wiki.termux.com/wiki/Termux:API">Termux APIの使い方</a>
