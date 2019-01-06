---
layout: default
title: Nodeの追加
---

Node-REDには有用なNodeが標準で付属していますが、
Node-REDプロジェクト以外のコミュニティでも
Node-REDにインストール可能なNodeが日々作られています。

利用可能なNodeは[Node-REDライブラリ](http://flows.nodered.org)や[npm repository](https://www.npmjs.com/browse/keyword/node-red)で探してみましょう。

### エディタの使い方

バージョン0.15からはエディタ上でNodeを管理することができます。
エディタ右上のメニューから `Manage Palette` を選択、続いてパレットの `Install` タブを選択、ここでインストールしたいNodeを検索してインストールすることができます。
また、パレットの `Nodes` タブで既存Nodeの有効/無効を切り替えることができます。

### npmパッケージングされたNodeのインストール方法

npmパッケージングされたNodeをインストールするには、
ユーザーデータディレクトリ内にローカルインストールするかグローバルインストールします (デフォルトのユーザーデータディレクトリ, `$HOME/.node-red`):

    cd $HOME/.node-red
    npm install <npm-package-name>

グローバルインストールの場合:

    sudo npm install -g <npm-package-name>

新しくインストールしたNodeを反映されるためにはNode-REDを再起動する必要があります。

### Nodeファイルを配置してインストールする方法

インストールするNodeの `.js` や `.html` ファイルを
ユーザーデータディレクトリ内の `nodes` ディレクトリにコピーすることによってNodeをインストールすることも可能です。
ただし、ユーザーデータディレクトリ内にインストールするNodeが依存しているnpmパッケージをインストールする必要があります。
これはNode開発時のみ推奨されます。
