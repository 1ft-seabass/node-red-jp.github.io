---
layout: default
title: Running on Raspberry Pi
---

このガイドでは、Node-REDをRespberry Piの上で使い始めるための2つの方法を紹介します。

  - November 2015バージョン以降の **Raspbian Jessie** full imageにプリインストールされているバージョンを利用
  - または、インストールスクリプトを利用し **手動でインストール** 

これで、 [エディターを使う](#using-the-editor) ことができるようになります。

## Raspbian Jessie

Raspbian JessieのNovember 2015バージョンでは、Node-REDは [RaspberryPi.org](https://www.raspberrypi.org/downloads/raspbian/) からダウンロードできるSDカードイメージにプリインストールされています。
これはnode.jsの古いバージョンを使用しており、以下のbashコマンドを使用して最新バージョンにアップグレードすることをお勧めします。

Jessieのminimalバージョンや、他のDebianベースを持っていて、Node-REDがまだインストールされていない場合は、Node-REDアップグレードスクリプトを使用してインストールまたはアップグレードできます。

    bash <(curl -sL https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-nodered)


**Note:**  **node.js 4.x または 6.x** へアップグレード済みの場合、プリインストール版Node-REDのアップグレードにapt-getは使用できません。

#### Upgrading

Node-REDバージョン0.14.x以降、アップグレードスクリプトはあらかじめインストールされており、最新のnodejs LTSおよびNode-REDの最新リリースバージョンへのフルアップグレードが行われます。

このスクリプトは多くのコマンドを *sudo* として実行し、既存のnodejsとコアのNode-REDディレクトリを削除します。

アップグレードする際は、一般ユーザー(通常はデフォルトユーザーの `pi`)で次のコマンドを実行します。

    update-nodejs-and-nodered

**Note:** 再起動時にシリアルポートノードが検知できない場合、アップデートコマンドを再度実行してください。updateコマンドが実行されないか見つからない場合は、上記のbashコマンドを使用します。

**自己責任でお願いします:** このスクリプトは利用可能な限られたextra nodeのインストールでのみテストされてます。また、既に `~/.node-red` ディレクトリへインストールされているネイティブプラグインと一緒にいくつかのノードもリビルドします。処理が失敗した場合、手動でのリビルドが必要になるか、以前インストールしたいくつかのノードの再インストールが必要になるかもしれません。

リビルドを行う:

    cd ~/.node-red
    npm rebuild

インストールされたノードのリストを見る:

    cd ~/.node-red
    npm ls --depth=0


#### Running Node-RED

Node-REDを開始するには、次のいずれかを行います。

  - Desktopにて、 `Menu -> Programming -> Node-RED` を選択
  - またはターミナルウィンドウから `node-red-start` を実行

**Note:** ウィンドウを閉じても (or ctrl-c) Node-REDは終了しません。バックグラウンドで稼働し続けます。

Node-REDを終了するには、 `node-red-stop` のコマンドを実行します。

#### Autostart on boot

Pi起動時にNode-REDを自動で開始するには次のコマンドを使用できます。

    sudo systemctl enable nodered.service


#### Adding nodes to preloaded version

デフォルトのインストールには含まれていないので、ノードを追加したい場合はまず `npm` ツールをインストールする必要があります。 node.js 4.xまたは6.xにアップグレードした場合はこの作業は必要ありません。

次のコマンドは `npm` をインストールし、最新の ` 2.x` バージョンにアップグレードします。

    sudo apt-get install npm
    sudo npm install -g npm@2.x
    hash -r
    cd ~/.node-red
    npm install node-red-{example node name}

**Note:** npmバージョン4は最新バージョンですが、現在は使用を推奨していません。


#### Next

次にNode-REDを再起動する必要があります。
これで、 [エディターを使う](#using-the-editor) ことができるようになります。

----

## Manual install

プリインストールでは、Debian Jessieのデフォルトnode.js（バージョン0.10.29）が使用されます。 手動でインストールする場合は、v6.xなどのより新しいバージョンのnode.jsを使用することをお勧めします。

Node.jsやその他の依存関係をインストールする最も簡単な方法は次のとおりです。

    sudo apt-get install build-essential python-rpi.gpio
    bash <(curl -sL https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-nodered)


その他のインストールオプションについては、 [main installation instructions](../getting-started/installation#install-node-red) を参照してください。

#### Accessing GPIO

GPIO (汎用入出力)のピンへアクセスする場合、 どのバージョンのPython RPi.GPIOライブラリーがインストールされているかを確認すべきです。

Node-REDにはハードウェアGPIOピンと対話するためのRaspberry Pi固有のスクリプトである `nrgpio` が含まれます。
npmモジュールをグローバルインストールした場合、このスクリプトは次の場所へ配置する必要があります。

    /usr/lib/node_modules/node-red/nodes/core/hardware/nrgpio ver
    or
    /usr/local/lib/node_modules/node-red/nodes/core/hardware/nrgpio ver

Pi2の場合は0.5.11以上、オリジナルPiの場合は0.5.8以上必要です。
そうでない場合、次のコマンドで利用可能な最新版をインストールします。

    sudo apt-get update && sudo apt-get install python-rpi.gpio

pi（またはroot）以外のユーザーとして実行する場合は、visudoを使用してsudoersに次の行を追加するなどして、そのユーザーを  [sudoers list](https://www.raspberrypi.org/documentation/linux/usage/users.md)  へ追加する必要があります。（またはpythonへアクセスする必要があるかもしれません）

    NodeREDusername ALL=(ALL) NOPASSWD: /usr/bin/python


### Starting Node-RED

Raspberry Piで使用できるメモリが限られているため、 `node-red-pi` コマンドでNode-REDを実行する必要があります。
これにより、Node.jsが未使用メモリを解放し始める時点を設定するための、追加の引数が提供可能になります。

`node-red-pi`スクリプトを実行する際は、`max-old-space-size` オプションを指定すべきです。

    node-red-pi --max-old-space-size=256

nodeコマンドを使用してNode-REDを直接実行する場合は、 `node` と `red.js` の間にこのオプションを指定する必要があります。

    node --max-old-space-size=256 red.js

このオプションを使用すると、クリーンアップ前に使用できる領域が256MBに制限されます。
あなたのPiに他の何も実行していないのであれば、その数字を256MBよりもさらに高くすることができるでしょう。
 コマンド `free -h`で、現在利用可能なメモリ量が分かります。

**Note:** `node-red-start`コマンドを使うRaspbian上のNode-REDのプリインストールバージョンもデフォルトで256MBに設定されています。 これを変更したい場合は、編集する必要のあるファイル（sudoとして）は `/lib/systemd/system/nodered.service` です。 手動でインストールされたバージョンにこれを追加する方法については、以下を参照してください。

#### Adding Autostart capability using SystemD

PiでNode-REDを自動起動する場合、組み込みのsystemd機能を使用するのが良いでしょう。 あらかじめインストールされたバージョンは、 `nodered.service`ファイルとスクリプトの開始と停止を使ってこれを行います。 次のコマンドを実行してこれらをインストールすることができます。

    sudo wget https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/nodered.service -O /lib/systemd/system/nodered.service
    sudo wget https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/node-red-start -O /usr/bin/node-red-start
    sudo wget https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/node-red-stop -O /usr/bin/node-red-stop
    sudo chmod +x /usr/bin/node-red-st*
    sudo systemctl daemon-reload

**Info:** これらのコマンドはroot（sudo）として実行されます。
ファイルを正しい場所にコピーし、2つのスクリプトを実行可能にしてから
systemdデーモンをリロードしてください。

Node-REDは、 `node-red-start`と` node-red-stop`コマンドを使用して起動および停止することができます。

次に、ブート時にクラッシュするたびにNode-REDを自動的に実行できるようにします。

    sudo systemctl enable nodered.service

以下のように無効にすることも可能です。

    sudo systemctl disable nodered.service


    sudo journalctl -f -u nodered -o cat

#### Changing the systemd environment - using a proxy

If you need to use a proxy for http requests - you need to set the *HTTP_PROXY* environment variable.
When using *systemd* this must be done within the service configuration. To edit this use sudo to edit the file `/lib/systemd/system/nodered.service` and add another `Environment=` line, for example:

http要求にプロキシーを使用する必要がある場合は、*HTTP_PROXY* 環境変数を設定する必要があります。
*systemd* を使用する場合、これはサービス構成内で行う必要があります。 sudoを使って `/lib/systemd/system/nodered.service` ファイルを編集し、別の `Environment =` の行を追加してください。例えば下記の通り。

    Nice=5
    Environment="NODE_OPTIONS=--max-old-space-size=128"
    Environment="HTTP_PROXY=my-proxy-server-address"

ファイルを保存し実行します。

    sudo systemctl daemon-reload

構成をリロードし、Node-REDを停止～リスタートします。

## Using the Editor

Node-REDが実行されたら、ブラウザをNode-REDが動作しているマシンにポイントします。
PiのIPアドレスを見つけるには、以下のコマンドを使用します。

    hostname -I

次に `http://{the-ip-address-returned}:1880/` を参照します。

<div class="doc-callout">
 <em>Note:</em> RaspbianのデフォルトブラウザであるEpiphanyにはいくつかの欠点があります。これは、特定のキーボードショートカットがNode-REDエディタで機能しないことを意味します。代わりにFirefox-ESRブラウザをインストールすることを<b>強く</b>お勧めします。
<pre>
    sudo apt-get install firefox-esr
</pre>
最近のビルドにはChromiumも含まれています。これもうまくいきますが、Pi1やZeroではかなり遅くなる可能性があります。
</div>

その後、[first flow](../getting-started/first-flow) を作成することが可能です。
## Extra Nodes

追加のノードをインストールする際は、自分のユーザディレクトリにいることを確認してください。デフォルトでは `〜/.node-red` です。

     cd ~/.node-red

There are some extra hardware specific nodes (e.g. for the Pibrella, PiFace and
LEDBorg plug on modules, Neopixel leds, temperature sensors, etc) available via the **[flows library](http://flows.nodered.org/)**.
For example the Pibrella node can be installed as follows

**[flows library](http://flows.nodered.org/)** 経由で入手可能な、ハードウェア固有のいくつかのノードがあります。（モジュール、Neopixel LED、温度センサーなどのPibrella、PiFace、LEDBorgプラグなど）
たとえば、Pibrellaノードは次のようにインストールできます。

    cd ~/.node-red
    npm install node-red-node-pibrella

新しいノードをロードするためにNode-REDを停止して再起動し、ブラウザのフローエディタページを更新する必要があります。

    node-red-stop
    node-red-start

エディタUI（メニュー → パレットの管理）を使用して追加ノードをインストールおよび削除することもできます。
※英語版では（Menu - Manage Palette）

## Interacting with the Pi hardware

ノードREDを使用してラズベリーパイと対話するにはいくつかの方法があります。

**rpi-gpio nodes**  (default)
: GPIOピンをモニタおよび制御するためにパレットに用意されています。 これは最も簡単で推奨される方法です。

**contrib-gpio nodes** (optional)
: BeagleBone、Arduino、Edisonなどのための一般的なgpioサポートを提供する@montesluの追加ノードです。これらは [ここ](https://github.com/monteslu/node-red-contrib-gpio) からインストールできます。

**wiring-pi module** (advanced)
: Functionノード内のGPIOピンやその他のデバイスへのより完全なアクセスを提供します。 これにより、ノード内にない他の機能への制御やアクセスが強化されますが、自分でプログラムを書く必要があります。


### rpi-gpio nodes

これらはPythonの `nrgpio` コマンドをコアインストールの一部として使用します。
これにより、Node-REDパレットのノードを介してGPIOピンを制御する方法が提供されます。


#### First Flow - Blink - gpio

GPIOヘッダーの11番ピンのLEDをトグルする「点滅」フローを実行するには、[ここ](https://projects.drogon.net/raspberry-pi/gpio-examples/tux-crossing/gpio-examples-1-a-single-led/) に記載されているようにLEDを接続する必要があります。

次のフローをコピーし、Import Nodesダイアログに貼り付けます。(ドロップダウンメニューから *インポートフォーム → クリップボード* 、またはCtrl-I)
[OK]をクリックしたら、ワークスペースをクリックして新しいノードを配置します。
※英語版では(ドロップダウンメニューから *Import From - Clipboard*)

        [{"id":"ae05f870.3bfc2","type":"function","name":"Toggle 0/1 on input","func":"\ncontext.state = context.state || 0;\n\n(context.state == 0) ? context.state = 1 : context.state = 0;\nmsg.payload = context.state;\n\nreturn msg;","outputs":1,"x":348.1666488647461,"y":146.16667652130127,"wires":[["1b0b73e9.14712c","b90e5005.a7c3b8"]]},{"id":"1b0b73e9.14712c","type":"debug","name":"","active":true,"x":587.1666488647461,"y":206.1666774749756,"wires":[]},{"id":"7aa75c69.fd5894","type":"inject","name":"tick every 1 sec","topic":"","payload":"","repeat":"1","crontab":"","once":false,"x":147.1666488647461,"y":146.1666774749756,"wires":[["ae05f870.3bfc2"]]},{"id":"b90e5005.a7c3b8","type":"rpi-gpio out","name":"","pin":"7","x":585.0000114440918,"y":146.00001049041748,"wires":[]}]


デプロイ(deploy)ボタンをクリックすると、フローが起動します。 1秒ごとにLEDのオンとオフが切り替わります。


### wiring-pi module

このセクションは上級ユーザー向けです。
通常、ほとんどのユーザーはこれを行う必要はありません。

このバージョンのRaspberry Piを使用すると、以前にインストールされたWiringPiライブラリへのnode.jsラッパーが使用されるため、Pi関数にアクセスするすべての関数に常にアクセスできるため、より複雑な作業を行うことができます。
ノードをドラッグして配置するのではなく、関数内にコードを記述する必要があります。

#### Installation

Node-REDをインストールしたら、次の [説明](http://wiringpi.com/download-and-install/) にしたがってWiring Piをインストールしてください。

#### Configuring Node-RED

Firstly the wiring-pi npm module needs to be installed into the same directory as your
`settings.js` file.
まず、 `settings.js` ファイルと同じディレクトリに、wiring-pi npmモジュールをインストールする必要があります。

    cd ~/.node-red
    npm install wiring-pi

これは、特定のノードをNode-REDに追加するものではありません。 代わりに、Wiring-Piモジュールを関数ノードで使用できるようにすることができます。

これを行うには、 `settings.js` ファイルを編集して `wiring-pi` モジュールをFunctionグローバルコンテキストセクションに追加します：

    functionGlobalContext: {
        wpi: require('wiring-pi')
    }

このモジュールは、 `context.global.wpi` として書くことができます。

#### Blink - Wiring-Pi

To run a "blink" flow that uses the WiringPi pin 0 - Pin 11 on the GPIO header,
you will need to connect up an LED as described [here](https://projects.drogon.net/raspberry-pi/gpio-examples/tux-crossing/gpio-examples-1-a-single-led/).

GPIOヘッダのWiringPiピン0〜11ピンを使用する「blink」フローを実行するには、[ここ](https://projects.drogon.net/raspberry-pi/gpio-examples/tux-crossing/gpio-examples-1-a-single-led/) に記載されているようにLEDを接続する必要があります。

次のフローをコピーし、Import Nodesダイアログに貼り付けます。(ドロップダウンメニューから *インポートフォーム → クリップボード* 、またはCtrl-I)
[OK]をクリックしたら、ワークスペースをクリックして新しいノードを配置します。
※英語版では(ドロップダウンメニューから *Import From - Clipboard*)

    [{"id":"860e0da9.98757","type":"function","name":"Toggle LED on input","func":"\n// select wpi pin 0 = pin 11 on header (for v2)\nvar pin = 0;\n\n// initialise the wpi to use the global context\nvar wpi = context.global.wpi;\n\n// use the default WiringPi pin number scheme...\nwpi.setup();\n\n// initialise the state of the pin if not already set\n// anything in context.  persists from one call to the function to the next\ncontext.state = context.state || wpi.LOW;\n\n// set the mode to output (just in case)\nwpi.pinMode(pin, wpi.modes.OUTPUT);\n\n// toggle the stored state of the pin\n(context.state == wpi.LOW) ? context.state = wpi.HIGH : context.state = wpi.LOW;\n\n// output the state to the pin\nwpi.digitalWrite(pin, context.state);\n\n// we don't \"need\" to return anything here but may help for debug\nreturn msg;","outputs":1,"x":333.16666412353516,"y":79.16666793823242,"wires":[["574f5131.36d0f8"]]},{"id":"14446ead.5aa501","type":"inject","name":"tick","topic":"","payload":"","repeat":"1","once":false,"x":113.16666412353516,"y":59.16666793823242,"wires":[["860e0da9.98757"]]},{"id":"574f5131.36d0f8","type":"debug","name":"","active":true,"x":553.1666641235352,"y":99.16666793823242,"wires":[]}]


デプロイ(deploy)ボタンをクリックすると、フローが起動します。 1秒ごとにLEDのオンとオフが切り替わります。
