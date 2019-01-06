---
layout: default
title: 起動
---

グローバルnpmパッケージとしてインストールした場合、
Node-REDは`node-red`コマンドで起動できます。:

    $ node-red

    Welcome to Node-RED
    ===================

    25 Mar 22:51:09 - [info] Node-RED version: v0.18.4
    25 Mar 22:51:09 - [info] Node.js  version: v8.11.1
    25 Mar 22:51:09 - [info] Loading palette nodes
    25 Mar 22:51:10 - [warn] ------------------------------------------
    25 Mar 22:51:10 - [warn] [rpi-gpio] Info : Ignoring Raspberry Pi specific node
    25 Mar 22:51:10 - [warn] ------------------------------------------
    25 Mar 22:51:10 - [info] Settings file  : /home/nol/.node-red/settings.js
    25 Mar 22:51:10 - [info] User Directory : /home/nol/.node-red
    25 Mar 22:51:10 - [info] Server now running at http://127.0.0.1:1880/
    25 Mar 22:51:10 - [info] Creating new flows file : flows_noltop.json
    25 Mar 22:51:10 - [info] Starting flows
    25 Mar 22:51:10 - [info] Started flows

次にNode-REDエディタにアクセスします。 <http://localhost:1880>.

以下は特定のハードウェア・プラットフォームで実行するためのガイドです。:

 - [Raspberry Pi](../hardware/raspberrypi)
 - [BeagleBone Black](../hardware/beagleboneblack)
 
#### 次のステップ

[最初のFlow](first-flow)を作成してみましょう。

---

### コマンドラインの使用方法

    使用方法: node-red [-v] [-?] [--settings settings.js] [--userDir DIR] [flows.json]

    オプション:
      -s, --settings FILE  設定ファイルのパスを指定する
      -u, --userDir  DIR   ユーザディレクトリのパスを指定する
      -v                   詳細ログの表示
      -?, --help           使用方法の確認

----

#### ローカルインストール時の実行方法 - Linux & Mac OS X

`node-red`コマンドはNode-REDをグローバルnpmパッケージとして
インストールしていなくても利用可能です。

コマンドスクリプトの実体はNode-REDをローカルnpmパッケージとしてインストールした場合は 
`npm install` したディレクトリから相対パスで `node_modules/node-red/bin/node-red` に存在します。
また、リリースされたzipファイルからインストールした場合は
zipファイルを展開したディレクトリから相対パスで `node-red-X.Y.Z/bin/node-red` に存在します。

まずは `node-red` コマンドに実行権限を与えます。:

    chmod +x <node-red-install-directory>/bin/node-red

するとNode-REDを起動できるようになります。:

    <node-red-install-directory>/bin/node-red

#### ローカルインストール時の実行方法 - Windows

Windowsの場合、`npm install`を実行したディレクトリ、
またはzipファイルを展開したディレクトリから次のコマンドを実行します。

    node node_modules/node-red/red.js

#### ユーザデータの保管

デフォルトのユーザディレクトリは `$HOME/.node-red` です。
Node-REDは後方互換性の理由からインストールディレクトリでユーザデータを発見した場合は優先的に参照します。
[アップグレード](upgrading)ドキュメントに
Node-REDインストールディレクトリからのデータ移行方法が含まれています。

ユーザディレクトリのパスは `--userDir` コマンドラインオプションで変更可能です。

#### Node-REDが動作するNode.jsプロセスへのパラメータの渡し方

メモリの使用量に制約があるRaspberry PiやBeagleBone Blackなどのデバイスで
Node-REDを動作させる場合は
Node-REDが動作するNode.jsプロセスにパラメータを渡す必要があります。

また、起動スクリプトは `node-red` コマンドの代わりに `node-red-pi` 利用しなければいけません。
_Note_: このスクリプトはWindowsでは利用できません。

`node` コマンドでNode-REDを起動している場合は
`red.js` の前に
NOde-REDに引き渡すパラメータを指定する必要があります。

具体的には以下のようなコマンドになります。:

    node-red-pi --max-old-space-size=128 --userDir /home/user/node-red-data/
    node --max-old-space-size=128 red.js --userDir /home/user/node-red-data/

### 起動時にNode-REDを起動する

開始/停止および起動時にアプリケーションを監視する方法が多くあります。
Raspberry Piのユーザには[Raspberry Piでの利用方法](../hardware/raspberrypi)に従うことを*強く*お勧めします。

Debian系（例 Ubuntu、Mint、Debianなど）のLinuxユーザは、
自分自身の*user id*および環境に合わせるため、ダウンロードした`/lib/systemd/system/nodered.service`ファイルを編集する必要がありますが、
[Adding Autostart capability using SystemD](../hardware/raspberrypi#adding-autostart-capability-using-systemd)の手順を利用することを推奨します。

以下のガイドは、大多数のユーザにとって最も素直だと考えている方法です。
Windowsユーザの場合、PM2はサービスとして自動的に実行されないので
以下の[NSSMオプション](#alternative-options)が良いかもしれません。

#### Using PM2

[PM2](https://github.com/Unitech/pm2)はNode.jsのプロセスマネージャです。
必要に応じて再起動を保証するように起動することも簡単にできます。

<div class="doc-callout"><em>Note</em> : PM2はGNU AGPL-3.0ライセンスの下で公開されています - 利用する前にライセンスの条件をご確認ください。</div>

##### 1. PM2をインストール

    sudo npm install -g pm2

<div class="doc-callout">
<em>Note</em> : Linux/OS X上でroot以外のユーザーとして実行している場合に <code>sudo</code> が必要です。
Windows上で実行している場合、
<code>sudo</code> コマンドなしで<a href="https://technet.microsoft.com/en-gb/library/cc947813%28v=ws.10%29.aspx">管理者としてコマンドシェルで実行</a>する必要があります。
</div>

<div class="doc-callout">
Windows上で実行している場合、
<a href="https://github.com/Unitech/PM2/blob/development/ADVANCED_README.md#windows">ここ</a>に記載されているように<code>tail.exe</code>がパス上に存在することを確認する必要があります。
</div>

##### 2. `node-red`コマンドの場所を確認

Linux/OS XにNode-REDをグローバルnpmインストールしている場合の `node-red`
コマンドの実体へのパスは `/usr/bin/node-red` か `/usr/local/bin/node-red` です。
いずれにしても `which node-red` コマンドで確認できます。

Node-REDをローカルnpmインストールしている場合は
`npm install` インストールしたディレクトリからの相対位置で `node_modules/node-red/bin/node-red` となります。

##### 3. PM2にNode-REDコマンドを指定

次のコマンドはPM2に `node-red` コマンドの位置を
`/usr/bin/node-red`として、通知しています。

Node-REDに渡したい任意のオプションは `--` の前に指定しなければいけません。

    pm2 start /usr/bin/node-red -- -v

<div class="doc-callout">
<em>Note</em> : メモリの使用量に制約があるRaspberry PiやBeagleBone Blackなどのデバイスで
Node-REDを動作させる場合はNode-REDが動作するNode.jsプロセスにパラメータを渡す必要があります。

<pre>pm2 start /usr/bin/node-red --node-args="--max-old-space-size=128" -- -v</pre>
</div>

<div class="doc-callout">
<em>Note</em> : ルートユーザとして実行したい場合、
ユーザデータの保管場所を特定するために`--userDir`オプションを利用する必要があります。
</div>

以下はバックグラウンドでの
Node-REDを起動しているプロセスに関する情報を表示したり、ログにアクセスしたりすることができます。:

    pm2 info node-red
    pm2 logs node-red

PM2のプロセス管理についての詳細は
[こちら](https://github.com/Unitech/pm2#process-management)をご覧ください。

##### 4. PM2起動スクリプト

PM2は実行されているプラットフォームに適した起動スクリプトを
作成することが可能です。

以下のコマンドを実行して提供される指示に従ってください。

    pm2 save
    pm2 startup

**systemd** を利用するには以下のようにします。

    pm2 startup systemd

<div class="doc-callout">
<em>Temporary Note:</em> PM2のGitHubに<a href="https://github.com/Unitech/PM2/issues/1321" target="_new">
open issue</a>があります。Linuxユーザは手動で<code>/etc/init.d/pm2-init.sh</code>ファイルを修正する必要があります。

<pre>export PM2_HOME="/root/.pm2"</pre>

上記部分を以下のように修正します。:

<pre>export PM2_HOME="/home/{youruser}/.pm2"</pre>
</div>

##### 5. 再起動

全てが期待通り起動したら再起動の確認もしてください。

#### その他の方法

他にも以下の様な様々なアプローチがあります。
以下の内容のいくつかはコミュニティメンバが作成しました。

 - [A systemd script (used by the Pi pre-install)](https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/nodered.service) by @NodeRED (linux)
 - [A systemd script](https://gist.github.com/Belphemur/3f6d3bf211b0e8a18d93) by Belphemur (linux)
 - [An init.d script](https://gist.github.com/bigmonkeyboy/9962293)  by dceejay (linux)
 - [An init.d script](https://gist.github.com/Belphemur/cf91100f81f2b37b3e94) by Belphemur (linux)
 - [A Launchd script](https://gist.github.com/natcl/4688162920f368707613) by natcl (OS X)
 - [Running as a Windows service using NSSM](https://gist.github.com/dceejay/576b4847f0a17dc066db) by dceejay
 - [Running as Windows/OS X service](http://www.hardill.me.uk/wordpress/2014/05/30/running-node-red-as-a-windows-or-osx-service/)  by Ben Hardill
