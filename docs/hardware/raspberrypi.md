---
layout: default
title: Raspberry Piで実行する
---

推奨されるRaspberry PiへのNode-REDのインストール方法は下記のスクリプトを利用することですが、Node-REDは[RaspberryPi.org](https://www.raspberrypi.org/downloads/raspbian/)からダウンロードできるRaspbian SDカードイメージにインストールされているRecommended Sofrwareアプリケーションからインストールできます。この方法でインストールした場合、下記のスクリプトを利用してアップグレードすることをお勧めします。

再起動スクリプトでNode-REDをアップグレードすることができます。

Node-REDはNode.js 4.xまたはそれ以前のバージョンをサポートしなくなりました。4.xをサポートしている最後のバージョンは0.19.6です。

### インストール/アップグレード

Raspbianバージョンを持っている場合、またはNode-REDがまだインストールされていない**Ubuntu**や**Diet-Pi**のような他の**Debian**ベースのインストールをおこなった場合、
Node-REDアップグレードスクリプトコマンドを利用してインストールまたはアップグレードをおこなうことができます。

    bash <(curl -sL https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-nodered)

Debianを最小インストールした場合、シリアルポートが正常にビルドおよびインストールされるため、スクリプトを実行する前に`sudo apt-get install build-essential`を実行する必要があるかもしれません。
これはオプションです。

#### スクリプトの解説

ノードはビルトインパレットマネージャで管理できるようになりました。しかし、デフォルトのRaspberry Piは事前にロードされている一部のノードがグローバルインストールされており、これらを管理および更新することは簡単にはできません。
このスクリプトの意図は...

 - 既存ユーザをLTS 8.xまたは10.x Node.jsおよび最新版Node-REDにアップグレードします
 - 既存のグローバルインストールされたノードがパレットマネージャによって管理できるようにユーザの~/.node-redへと移動します
 - オプションで、Raspbian Piフルイメージにプリインストールされている追加ノードを（再）インストールします
 - Note: ユーザがインストールした既存ノードは更新されません。これはユーザによって手動で更新される必要があります（下記を参照してください）。

Raspberry Piユーザを対象としていますが、スクリプトはUbuntuのようなDebianベースのオペレーティングシステムでも実行できるため、
他のハードウェアプラットフォームでも利用できますが、テストを幅広くおこなってはいません。

上述のコマンドは**sudo**で多くのコマンドを実行し、既存のNode.jsおよびNode-REDのコアディレクトリを削除します。https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/update-nodejs-and-noderedにアクセスして確認をおこなってください。

スクリプトは次の手順で実行されます。

 - Raspberry Piの追加ノードを（再）インストールするかどうかを質問します。
 - /usr/lib/node_modules内のグローバルインストールされた*node-red-*ノードのリストを保存します。
 - apt-get remove nodered
 - /usr/binおよび/usr/local/binからNode-REDバイナリを削除します。
 - /usr/lib/node_modulesおよび/usr/local/lib/node_modulesからNode-REDモジュールを削除します。
 - Node.jsがNode.jsパッケージまたはDebianのどちらからインストールされたかを検出します。
 - v8以降ではない場合 - 適切に削除し、最新のv8またはv10 LTSをインストールします（aptを利用せずに）。
 - 以前のバージョンのコードを削除するため、npmキャッシュおよび.node-gypキャッシュをクリーンします。
 - 最新版Node-REDをインストールします。
 - 以前グローバルインストールされていたノードをユーザアカウントで再インストールします。
 - 必要であれば、Raspberry Piの追加ノードを再インストールする。
 - 全ノードを再ビルドします。 - 最新版のNode.jsに適合するようにバイナリを再コンパイルします。
 - /usr/binにnode-red-start、node-red-stopおよびnode-red-logコマンドを追加します。
 - メニューショートカットとアイコンを追加します。
 - systemdスクリプトを追加し、ユーザを設定します。
 - Raspberry Piの場合、IoT exampleにCPU temperatureを追加します。

**利用者責任** - このスクリプトは少数の利用可能な追加ノードを含むインストールでのみテストされています。
このスクリプトは`~/.node-red`ディレクトリ内にインストールしたネイティブプラグインでノードを再ビルドしようとします。
これに失敗した場合、
手動で再ビルドまたは以前インストールしたノードを再インストールする必要があります。
再ビルドするには:

    cd ~/.node-red
    npm rebuild

インストールしたノードのリストを表示するには:

    cd ~/.node-red
    npm ls --depth=0

**Note**: 以前にローカルインストールしたノードは更新されません。更新するにはユーザディレクトリに移動し、以下のコマンドを実行します。一部のノードのバージョンはこのディレクトリ内の`package.json`ファイルによって固定されています。 - `npm update`を実行する前に必要なバージョンを選択するため、手動で編集する必要があるかもしれません。

    cd ~/.node-red
    npm outdated
    npm update


#### SDカードを交換する

異なるバージョンのRaspberry Piでは異なるARMプロセッサが利用されています。ARM6とARM7でNode.jsバイナリは異なります。例えばRaspberry Pi ZeroからRaspberry Pi 3への変更のようにARM6からARM7にSDカードを交換することは問題ありません。Node.jsをアンイストールし、適切なバージョンのNode.jsをインストールするためにアップグレードスクリプトを再実行する以外、他の方法による交換では動作しません。


#### Node-REDを起動する

Node-REDを実行するには、以下のいずれかをおこないます。:

  - デスクトップでは、`Menu -> Programming -> Node-RED`を選択します。
  - または、新規のターミナルウィンドウで`node-red-start`を実行します。

*Note:* ウィンドウを閉じること（もしくはctrl-c）では、Node-REDの実行は停止しません。バックグラウンドで実行されたままです。

Node-REDを停止するには、`node-red-stop`コマンドを実行します。

ログを確認するには、`node-red-log`コマンドを実行します。


#### 起動時に自動起動する

Raspberry Piを起動した際にNode-REDを起動したい場合、以下のコマンドを利用します。

    sudo systemctl enable nodered.service

また同様に、起動時の自動起動を無効化するためには`sudo systemctl disable nodered.service`を利用します。


#### プリロードされたRaspbianバージョンにノードを追加する

上述のスクリプトを利用してプリインストールバージョンを最新バージョンに**アップグレードしていない**場合、
追加のノードを加えるためにはまず`npm`ツール、デフォルトのRaspbianインストールには含まれていないため、をインストールする必要があります。
上述のスクリプトを利用してアップグレードした場合、これは不要です。

以下のコマンドは`npm`をインストールし、そして最新バージョンにアップグレードします。

    sudo apt-get install npm
    sudo npm install -g npm
    hash -r
    cd ~/.node-red
    npm install node-red-{example node name}


#### そして次に

その後、Node-REDを再起動する必要があります。
[エディタを利用する](#using-the-editor)ことを始められます。

----

## 注意

Debian/Raspbian Wheezyは現在「EOL」を過ぎており、つまりもはやサポートされていません、
そしてこのドキュメントは最低でもJessieを対象としています。

### GPIOにアクセスする

Node-REDによってGPIOピンにアクセスしようとする場合、どのバージョンのPython RPi.GPIOライブラリがインストールされているかを確認すべきです。

Node-REDは、ハードウェアGPIOピンと通信するためにRaspberry Pi固有のスクリプト`nrgpio`を持っています。
グローバルnpmモジュールとしてインストールしている場合、このスクリプトは以下の位置にあるはずです。:

    /usr/lib/node_modules/node-red/nodes/core/hardware/nrgpio ver
    または
    /usr/local/lib/node_modules/node-red/nodes/core/hardware/nrgpio ver

Raspberry Pi 2では最低でも0.5.11またはオリジナルのRaspberry Piでは0.5.8である必要があります。
そうではない場合、次のコマンドで最新のものがインストールされます。:

    sudo apt-get update && sudo apt-get install python-rpi.gpio

また、ユーザは`gpio`グループのメンバーでなければなりません。このグループに現在のユーザを追加するには、

    sudo adduser $USER gpio

pi（またはroot）以外のユーザとして実行したい場合、
そのユーザを[sudoers list](https://www.raspberrypi.org/documentation/linux/usage/users.md)に追加する - 
もしくは - 例えばvisudoを利用することで以下をsudoersに追加することで単にpythonにアクセスする必要があります。

    NodeREDusername ALL=(ALL) NOPASSWD: /usr/bin/python


### Node-REDを起動する

Raspberry Piでは使用可能なメモリが制限されているため、
`node-red-pi`コマンドによってNode-REDを起動する必要があります。
これによって、Node.jsが未使用のメモリを解放し始めるポイントを設定する追加引数を指定できます。

`node-red-pi`スクリプトによって起動するとき、
`max-old-space-size`オプションを指定する必要があります。:

    node-red-pi --max-old-space-size=256

nodeコマンドを直接使用してNode-REDを起動する場合、
このオプションはnodeとred.jsの間になければなりません。

    node --max-old-space-size=256 red.js

このオプションは、クリーンアップする前に使用できるスペースを256MBに制限します。
Raspberry Piで何も実行していない場合、
この値を256そしておそらくそれ以上に増やすことができます。
`free -h`コマンドは現在使用可能なメモリ量に関する手がかりを与えてくれます。

**Note**: `node-red-start`を利用したRaspbianにプリインストールされているNode-REDバージョンでもデフォルトで256MBに設定されています。
これを変更したい場合、
（sudoとして）編集する必要があるファイルは`/lib/systemd/system/nodered.service`です。
どのようにこれを手動インストール版に追加するのかについては以下を参照してください。

#### SystemDを使用して自動起動機能を追加する

Node-REDを自動起動するための推奨方法は、ビルトインsystemd機能を利用することです。
プリインストール版では、
`nodered.service`ファイルを使用してスクリプトを起動および停止します。
以下のコマンドを実行することでこれらをインストールすることができます。

    sudo wget https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/nodered.service -O /lib/systemd/system/nodered.service
    sudo wget https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/node-red-start -O /usr/bin/node-red-start
    sudo wget https://raw.githubusercontent.com/node-red/raspbian-deb-package/master/resources/node-red-stop -O /usr/bin/node-red-stop
    sudo chmod +x /usr/bin/node-red-st*
    sudo systemctl daemon-reload

**Info:** これらのコマンドはroot（sudo）として実行されます。
- 3つの必要なファイルを正しい位置にダウンロード、
2つのファイルを実行可能にし、systemdデーモンをリロードします

`node-red-start`および`node-red-stop`コマンドを使用することで
Node-REDを起動および停止することができます。

そして、起動時およびクラッシュ時にNode-REDが自動的に実行されることを可能にするには

    sudo systemctl enable nodered.service

以下のようにすることで無効にできます。

    sudo systemctl disable nodered.service

systemdはログをとるために`/var/log/system.log`を利用します。ログをフィルタするには

    sudo journalctl -f -u nodered -o cat

#### systemd環境を変更する -  piではないユーザとして実行する

デフォルトの`pi`以外のユーザとして実行するため、nodered.serviceを編集する必要があります。これを編集するには、`/lib/systemd/system/nodered.service`ファイルを編集するためにsudoを使用し、以下の{your_user}と指定されている行を変更します。

    [Service]
    Type=simple
    # Run as normal pi user - change to the user name you wish to run Node-RED as
    User={your_user}
    Group={your_user}
    WorkingDirectory=/home/{your_user}
    Nice=5
    Environment="PI_NODE_OPTIONS=--max_old_space_size=256"
    ...

ファイルを保存し、実行します。:

    sudo systemctl daemon-reload

#### systemd環境を変更する - プロキシを利用する

HTTPリクエストのためにプロキシを利用する必要がある場合、 - *HTTP_PROXY*環境変数を設定する必要があります。
*systemd*を使用する場合、これはサービス設定内でおこなわなければなりません。これを編集するためには、`/lib/systemd/system/nodered.service`ファイルを編集するためにsudoを使用し、別の`Environment=`の行を、以下のように追加します。:

    ...
    Nice=5
    Environment="NODE_OPTIONS=--max-old-space-size=128"
    Environment="HTTP_PROXY=my-proxy-server-address"
    ...

ファイルを保存し、実行します。:

    sudo systemctl daemon-reload

設定をリロードします。Node-REDを停止し、再起動します。

## エディタを利用する

Node-REDが実行されたら、 - Node-REDが起動しているマシンのブラウザを起動します。
Raspberry PiのIPアドレスを確認する方法の1つは、このコマンドを利用することです。

    hostname -I

そしてブラウザで`http://{the-ip-address-returned}:1880/`を閲覧します。

<div class="doc-callout">
 <em>Note:</em> Raspbianに含まれるデフォルトのブラウザは、以前はEpiphanyでしたが、
Node-REDエディタ内では特定のキーボードショートカットが機能しないという奇妙な点がいくつかあります。
代わりにFirefox-ESRまたはChromiumブラウザをインストールすることを<b>強く</b>推奨します。
<pre>
    sudo apt-get install firefox-esr
</pre>
最近のビルドにはChromium yがデフォルトで含まれています。 - これも正常に動作しますがRaspberry Pi 1やZeroではかなり遅くなる可能性があります。
</div>

[はじめてのフロー](../getting-started/first-flow)の作成を始めることができます。

## 追加ノード

エディタUI、メニュー - パレット管理を利用して追加ノードをインストールできます。追加ノードを手動でインストールするため、ユーザディレクトリ、デフォルトでは`~/.node-red`にいることを確認してください。

     cd ~/.node-red

いくつかのハードウェア固有の追加ノード（例えばPibrella、PiFace、LEDBorgプラグオンモジュール、Neopixel LED、温度計など）を
**[フローライブラリ](http://flows.nodered.org/)**を介して利用できます。
例えば、リモートアクセスを許可し、サーボを作動できる代替PiGPIOdノードは、
以下のようにインストールすることができます。

    cd ~/.node-red
    npm install node-red-node-pi-gpiod

新しいノードをロードするため、Node-REDを停止し再起動、そしてブラウザのフローエディタのページを更新する必要があります。

    node-red-stop
    node-red-start


## Raspberry Piハードウェアと通信する

Node-REDを利用してRaspberry Piと通信するにはいくつかの方法があります。

**rpi-gpioノード**  (デフォルト)
: GPIOピンを監視および制御するためにパレットに用意されています。
  これが最も簡単で推奨される方法です。

**contrib-gpioノード** (オプション)
: @montesluから提供された、Raspberry Pi、BeagleBone、Arduino、Edisonなどに対する一般的なGPIOをサポートする追加ノードです。  [こちら](https://github.com/monteslu/node-red-contrib-gpio)からインストールが可能です。

**wiring-piモジュール** (上級)
: これはFunctionノード内でGPIOピンや他のデバイスへの更に完璧なアクセスを可能にします。
  ノードにはない他の機能についてのより多くの制御とアクセスを可能にしますが、
  自分自身でプログラムしなければなりません。


### rpi-gpioノード

これらのノードはコアインストールの一部としてpython `nrgpio`コマンドを使用します。
Node-REDパレット内のノードを介してGPIOピンを制御する方法を提供します。ユーザはGPIOグループに所属している必要があります。

    sudo adduser $USER gpio


#### はじめてのフロー - 点滅 - gpio

GPIOヘッダのピン11のLEDを付けたり消したりする「点滅」フローを実行するため、
[こちら](https://projects.drogon.net/raspberry-pi/gpio-examples/tux-crossing/gpio-examples-1-a-single-led/)で解説しているようにLEDに接続する必要があります。

そして、以下のフローをコピーし、インポートノードダイアログに貼り付けます
（ドロップダウンメニューで*Import From - Clipboard*、またはCtrl-I）。
OKをクリックした後、ワークスペース内をクリックして新しいノードを配置します。


        [{"id":"ae05f870.3bfc2","type":"function","name":"Toggle 0/1 on input","func":"\ncontext.state = context.state || 0;\n\n(context.state == 0) ? context.state = 1 : context.state = 0;\nmsg.payload = context.state;\n\nreturn msg;","outputs":1,"x":348.1666488647461,"y":146.16667652130127,"wires":[["1b0b73e9.14712c","b90e5005.a7c3b8"]]},{"id":"1b0b73e9.14712c","type":"debug","name":"","active":true,"x":587.1666488647461,"y":206.1666774749756,"wires":[]},{"id":"7aa75c69.fd5894","type":"inject","name":"tick every 1 sec","topic":"","payload":"","repeat":"1","crontab":"","once":false,"x":147.1666488647461,"y":146.1666774749756,"wires":[["ae05f870.3bfc2"]]},{"id":"b90e5005.a7c3b8","type":"rpi-gpio out","name":"","pin":"7","x":585.0000114440918,"y":146.00001049041748,"wires":[]}]


デプロイボタンをクリックし、フローを実行します。
LEDは1秒に1回オンとオフが切り替わります。


### wiring-piモジュール

このセクションは上級ユーザ向けです。
一般に、ほとんどのユーザはこれをおこなう必要はありません。

このバージョンのRaspberry Piでは、
以前インストールしたWiringPiライブラリへのNode.jsラッパーを使用しているため、
作成したすべての関数に常にRaspberry Piの機能へのアクセス権が付与されるため、
面倒なことはありません。
ノードを配置して繋げるのではなく、必ずFunctionノード内にコードを書いてください。

#### インストール

Node-REDをインストールした後、
wiring-piモジュールをインストールするためにこちらの[手順](http://wiringpi.com/download-and-install/)にしたがってください。

#### Node-REDを設定する

まず、wiring-pi npmモジュールは、
`settings.js`ファイルと同じディレクトリにインストールする必要があります。

    cd ~/.node-red
    npm install wiring-pi

これはNode-REDに特定のノードを追加しません。
代わりにwiring-piモジュールがFunctionノード内で利用できるようになります。

これを実施するためには、`settings.js`ファイルを編集し、
function global contextセクションに`wiring-pi`モジュールを追加します。

    functionGlobalContext: {
        wpi: require('wiring-pi')
    }

そして、このモジュールは`context.global.wpi`とすることによって、どのFunctionノードでも利用可能です。

#### 点滅 - Wiring-Pi

WiringPiのピン0 - GPIOヘッダにピン11を利用する「点滅」フローを実行するため、
[こちら](https://projects.drogon.net/raspberry-pi/gpio-examples/tux-crossing/gpio-examples-1-a-single-led/)で説明されているようにLEDと接続する必要があります。

そして、以下のフローをコピーし、インポートノードダイアログに貼り付けます
（ドロップダウンメニューで*Import From - Clipboard*、またはCtrl-I）。
OKをクリックした後、ワークスペース内をクリックして新しいノードを配置します。

    [{"id":"860e0da9.98757","type":"function","name":"Toggle LED on input","func":"\n// select wpi pin 0 = pin 11 on header (for v2)\nvar pin = 0;\n\n// initialise the wpi to use the global context\nvar wpi = context.global.wpi;\n\n// use the default WiringPi pin number scheme...\nwpi.setup();\n\n// initialise the state of the pin if not already set\n// anything in context.  persists from one call to the function to the next\ncontext.state = context.state || wpi.LOW;\n\n// set the mode to output (just in case)\nwpi.pinMode(pin, wpi.modes.OUTPUT);\n\n// toggle the stored state of the pin\n(context.state == wpi.LOW) ? context.state = wpi.HIGH : context.state = wpi.LOW;\n\n// output the state to the pin\nwpi.digitalWrite(pin, context.state);\n\n// we don't \"need\" to return anything here but may help for debug\nreturn msg;","outputs":1,"x":333.16666412353516,"y":79.16666793823242,"wires":[["574f5131.36d0f8"]]},{"id":"14446ead.5aa501","type":"inject","name":"tick","topic":"","payload":"","repeat":"1","once":false,"x":113.16666412353516,"y":59.16666793823242,"wires":[["860e0da9.98757"]]},{"id":"574f5131.36d0f8","type":"debug","name":"","active":true,"x":553.1666641235352,"y":99.16666793823242,"wires":[]}]


デプロイボタンをクリックし、フローを実行します。
LEDは1秒に1回オンとオフが切り替わります。
