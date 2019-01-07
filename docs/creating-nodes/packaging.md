---
layout: docs
toc: creating-nodes-toc.html
title: パッケージング
---

ノードはモジュールとしてパッケージ化し、npmレポジトリに公開することができます。
これにより、インストールするのに必要な依存関係と共にインストールが容易になります。

### ディレクトリ構造

以下に、ノードパッケージの典型的なディレクトリ構造を示します:

    - package.json
    - sample
       |-sample.html
       |-sample.js
       \-icons
          \-sample.png
    - README.md
    - LICENSE

パッケージ内で使用されるディレクトリ構造に厳密な要件はありません。
パッケージに複数のノードが含まれている場合、それらはすべて同じディレクトリに存在することができますが、
それぞれが独自のサブディレクトリに配置することもできます。

### ノードモジュールをローカルでテストする

ノードモジュールをローカルでテストするには、`nmp link`コマンドを使うことができます。
これにより、ローカルディレクトリにノードを作成し、ローカルのNode-REDインストールにリンクさせることができます。
あたかもnpmがインストールされているかのように。

1.ノードの`package.json`ファイルを含むディレクトリで、以下を実行します。: `sudo npm link`
2. あなたの node-red ユーザディレクトリでは、通常は`~/.node-red`が実行されます。: `npm link <name of node module>`

これにより、2つのディレクトリ間に適切なシンボリックリンクが作成され、
Node-REDは起動時にノードを検出します。
Node-REDを再起動するだけで、ノードのファイルに対する変更を取得することができます。

### package.json

通常の入力と並んで、`package.json`ファイルには、
モジュールが提供するノードを認識する`node-red`エントリが含まれていなければなりません。
それらの`.js`ファイルへのポインタとともに表示されます。

いずれかのノードが他のnpmモジュールと依存している場合、
それらは`dependencies`プロパティに含まれていなければなりません。

npmレポジトリ内でノードを発見可能にするために、
ファイルは`keywords`プロパティに`node-red`を含める必要があります。
これにより、[キーワードで検索する](https://www.npmjs.org/browse/keyword/node-red)際にパッケージが確実に表示されます。

<div class="doc-callout"><em>Note</em>: ノードが安定し正しく動作するまで
`node-red`キーワードを追加しないでください。
他の人がそれを使用出来るように十分に文書化されています。</div>

{% highlight json %}
{
    "name"         : "node-red-contrib-samplenode",
    "version"      : "0.0.1",
    "description"  : "A sample node for node-red",
    "dependencies": {
    },
    "keywords": [ "node-red" ],
    "node-red"     : {
        "nodes": {
            "sample": "sample/sample.js"
        }
    }
}
{% endhighlight %}

### ネーミング

ノードモジュールは、Node-REDプロジェクトによって維持されていないことを明確にするために、
`node-red-contrib-`を名前のプレフィックスとして使用する必要があります。
あるいは、`node-red`をプレフィックスとして使用しない任意の名前を使用することができます。

### README.md

README.mdファイルには、
ノードの機能・それを機能させるために必要な前提条件を列挙します。
ノードのhtmlファイルの、
*info*タブ部分に含まれていない追加の指示や、
それを使用している小さなサンプルフローを含めると便利かもしれません。

ファイルは次のようにマークアップする必要があります。
[GitHub flavoured markdown](https://help.github.com/articles/markdown-basics/).

### ライセンス

他の人が自分のコードで出来ることと出来ない事を知ることができるように、
ライセンスファイルを含めてください。

### npmに公開する

npmレポジトリにパッケージを公開するガイドがたくさんあります。
[こちら](https://docs.npmjs.com/misc/developers)に基本的な概要があります。

### flows.nodered.orgに追加する

必要なすべてのものが適切だった場合、
[Node-RED Flow Library](https://flows.nodered.org)は、数時間以内に自動的に作成したノードを登録します。
数時間を過ぎても表示されない場合、[Node-REDフォーラム](https://discourse.nodered.org)
または[Slack](https://nodered.org/slack)で助言を求めることができます。
