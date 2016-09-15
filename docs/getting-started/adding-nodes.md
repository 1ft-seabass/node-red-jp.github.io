---
layout: default
title: Nodeの追加
---

Node-REDは有用なNodeが標準で付属していますが、Node-REDプロジェクト以外のコミュニティでもNode-REDにインストール可能なNodeが日々増加しています。

利用可能なNodeは[Node-REDライブラリ](http://flows.nodered.org)や[npm repository](https://www.npmjs.com/browse/keyword/node-red)で探してみましょう。

### npmパッケージングされたNodeのインストール方法

npmパッケージングされたNodeをインストールするには、ユーザーデータディレクトリ内にローカルインストールするかグローバルインストールすます (デフォルトのユーザーデータディレクトリ, `$HOME/.node-red`):

    cd $HOME/.node-red
    npm install <npm-package-name>

グローバルインストールの場合:

    sudo npm install -g <npm-package-name>

新しくインストールしたNodeを反映されるためにはNode-REDを再起動する必要があります。

### Nodeファイルを配置してインストールする方法

ユーザーデータディレクトリ内のノードのディレクトリにインストールするNodeの `.js` や ``.html` ファイルをコピーすることによってNodeをインストールすることも可能です。ただし、インストールするNodeが依存しているnpmパッケージはユーザーデータディレクトリ内にインストールされている必要があります。
