---
layout: docs
toc: user-guide-toc.html
title: Coreノード
---

Node-REDのパレットには、フローを作成するための基本的な構成要素であるデフォルトのノードが一通り含まれています。
このページでは知っておくべきCoreノードについて取り上げます。

すべてのノードは、ノードを選択した際にInfoサイドバーで確認できるドキュメントを含んでいます。

- [Inject](#inject)
- [Debug](#debug)
- [Function](#function)
- [Change](#change)
- [Switch](#switch)
- [Template](#template)

***

<h3 id="inject"><img alt="Inject node" style="vertical-align: middle" src="/docs/user-guide/images/node_inject.png" width="169px"></h3>

Injectノードは、エディタ内でこのノードのボタンをクリックすることで
手動でフローを始動させるために利用できます。
一定間隔でフローを自動的に始動させることもできます。

Injectノードから送出されるメッセージは、
その`payload`および`topic`プロパティを設定することができます。

`payload`には様々な型を設定することができます。:

 - フローまたはグローバルコンテキストのプロパティ値
 - 文字列型、数値型、Boolean型、バッファ型、オブジェクト型
 - 1970年1月1日からのミリ秒で表現されるタイムスタンプ型

***

<h3 id="debug"><img alt="Debug node" style="vertical-align: middle" src="/docs/user-guide/images/node_debug.png" width="169px"></h3>

Debugノードは、エディタ内のDebugサイドバーにメッセージを表示するために利用することができます。

このサイドバーは送られてきたメッセージを構造的なビューで表示し、
メッセージを探し出しやすくします。

各メッセージと並んで、
Debugサイドバーにはメッセージを受信した時刻とそのメッセージを送出したDebugノードの情報が含まれています。
ソースノードIDをクリックするとワークスペース内のどのノードかがわかります。

ノードのボタンは、出力の有効化無効化を制御するために使用されます。
利用していないDebugノードは無効化したり、削除したりすることを推奨します。

また、このノードはランタイムログにすべてのメッセージを出力したり、
Debugノードの下に短い（32文字）のステータステキストを表示させたりすることができます。

[メッセージを利用する](/docs/user-guide/messages)ページでは
Debugサイドバーの使い方について更なる情報を提供しています。

***

<h3 id="function"><img alt="Function node" style="vertical-align: middle" src="/docs/user-guide/images/node_function.png" width="169px"></h3>

Functionノードは、
受け渡されるメッセージに対するJavaScriptコードの実行を可能にします。

Functionノードを利用するための素晴らしいガイドは、[こちら](/docs/writing-functions)にあります。

***

<h3 id="change"><img alt="Change node" style="vertical-align: middle" src="/docs/user-guide/images/node_change.png" width="169px"></h3>

Changeノードは、Functionノードに頼ることなく、
メッセージプロパティを変更したり、コンテキストプロパティを設定したりすることができます。

各ノードには、順番に適用される複数の操作を設定することが可能です。
利用できる操作は以下のとおりです。:

 - **値の代入** - プロパティの代入。
   値にはさまざまな型を利用でき、さらに既存のメッセージまたはコンテキストプロパティから取得することもできます。
 - **値の置換** - メッセージプロパティの一部を検索、置換
 - **値の移動** - プロパティの移動や名称変更
 - **値の削除** - プロパティの削除

プロパティを設定する際には[JSONata式](https://jsonata.org)の結果を設定することもできます。
JSONataは、JSONデータに対する軽量なクエリかつ変換言語です。

***

<h3 id="switch"><img alt="Switch node" style="vertical-align: middle" src="/docs/user-guide/images/node_switch.png" width="169px"></h3>

Switchノードは各メッセージに対して一連のルールで評価し、
メッセージを異なるフローにルーティングすることができます。

このノードには、
評価するプロパティ（メッセージプロパティまたはコンテキストプロパティ）を設定します。

ルールには以下の4種類があります。:

 - **Value rule**は設定されたプロパティに対して評価をおこないます
 - **Sequence rule**はSplitノードによって生成されるような
   メッセージシーケンスを利用することができます
 - **JSONata式**ではメッセージ全体に対して評価をおこない、
   `true`の値を返した場合に一致したとみなすことができます
 - **その他**は前述のルールのどれにも一致しなかった場合、
   一致するように利用できます

このノードは、一致したルールに応じた出力先すべてに対してメッセージを送出します。
しかし、一致するルールがあった時点で評価をやめるように設定することもできます。

***

<h3 id="template"><img alt="Template node" style="vertical-align: middle" src="/docs/user-guide/images/node_template.png" width="169px"></h3>

Templateノードは、メッセージプロパティをテンプレートに設定することで
テキストを生成することができます。

このノードでは、
[Mustache](https://mustache.github.io/mustache.5.html)記法を利用することができます。

例えば、テンプレートは以下のようにします。:

{% raw %}
```
This is the payload: {{payload}} !
```
{% endraw %}

`{% raw %}{{payload}}{% endraw %}`はメッセージの`payload`プロパティの値に置換されます。

デフォルトではMustache記法は特定の文字をHTMLエスケープ文字コードに置換します。
これを防ぐためには、3つの波括弧を利用することができます。: `{% raw %}{{{payload}}}{% endraw %}`.

Mustache記法は単純なループをサポートしています。
例えば、`msg.payload`が名前の配列を有していた場合（`["Nick", "Dave", "Claire"]`のような）、
以下のテンプレートによって名前のHTMLリストを生成することができます。:

{% raw %}
```
<ul>
{{#payload}}
  <li>{{.}}</li>
{{/payload}}
</ul>
```
{% endraw %}


```
<ul>
  <li>Nick</li>
  <li>Dave</li>
  <li>Claire</li>
</ul>
```

このノードはテンプレート対して設定されているメッセージまたはコンテキストプロパティを代入します。
もしテンプレートが適切なJSONやYAMLコンテンツを生成する場合、
生成物をJavaScriptオブジェクトにパースされるように設定することができます。

***
