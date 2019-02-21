---
layout: docs
toc: user-guide-toc.html
title: コンテキストを利用する
---

 - [コンテキストとは](#コンテキストとは)
 - [コンテキストスコープ](#コンテキストスコープ)
 - [コンテキストに保管する](#コンテキストに保管する)
   - [Saving context data to the file-system](#saving-context-data-to-the-file-system)
   - [Using multiple context stores](#using-multiple-context-stores)
 - [フローでコンテキストを利用する](#フローでコンテキストを利用する)
 - [Functionノードでコンテキストを利用する](#functionノードでコンテキストを利用する)
 - [カスタムノードでコンテキストを利用する](#カスタムノードでコンテキストを利用する)

### コンテキストとは？

Node-REDは、フローで受け渡すメッセージを利用せずに異なるノード間で共有する情報を保管する方法を提供しています。
これは「コンテキスト」と呼ばれます。

### コンテキストスコープ

特定のコンテキスト値の「スコープ」によって、誰と共有されるかが決定されます。
コンテキストスコープには3つのレベルが存在します。:

 - ノード - 値を設定したノードのみアクセスできます
 - フロー - 同じフロー（またはエディタのタブ）のすべてのノードがアクセスできます
 - グローバル - すべてのノードがアクセスできます

特定の値のスコープの選択は、その値がどのように利用されるかに依ります。

もし値が単一のノードからのアクセスだけで足りる場合、例えばFunctionノードなどの場合、
ノードコンテキストで充分です。

しばしば、コンテキストはある種の状態を複数のノード間で共有することができます。
例えば、センサーは定期的に1つのフローに値を送出しており、
最新の値を返却するために別のHTTPトリガーフローを作成したいかもしれません。
センサーの測定値をコンテキストに格納することで、HTTPフローが戻ることが可能になります。

設定ファイルの`functionGlobalContext`プロパティを利用することで、
グローバルコンテキストに事前に値を設定することができます。

<div class="doc-callout"><em>Note</em> : for nodes in a subflow, the <code>flow</code>
context is shared by those nodes and not the flow the subflow is on.
From Node-RED 0.20, the nodes inside a subflow can access the context of the
parent flow by prepending <code>$parent.</code> to the context key. For example:
<pre>var colour = flow.get("$parent.colour");</pre></div>


### コンテキストに保管する

デフォルトでは、コンテキストはメモリのみに保存されます。
つまり、Node-REDが再起動するたびにコンテキストの内容はクリアされます。
0.19版リリースによって、コンテキストデータを保存するようにNode-REDを設定することが可能になり、再起動後も利用できるようになりました。

`setting.js`の`contextStorage`プロパティは、
コンテキストデータがどのように保管されるかを設定するために利用できます。

Node-RED provides two built-in modules for this: `memory` and `localfilesystem`.
It is also possible to create custom store plugins to save the data elsewhere.

#### Saving context data to the file-system

To enable file-based storage, the following option can be used:

```javascript
contextStorage: {
   default: {
       module: "localfilesystem"
   }
}
```

This sets the default context store to be an instance of the `localfilesystem`
plugin, with all of its default settings. That means:

 - it will store the context data in files under `~/.node-red/context/`
 - it caches the values in memory and only writes them out to the file-system
   every 30 seconds.

<div class="doc-callout"><em>Note</em> : Depending on when you installed Node-RED,
your <code>settings.js</code> file may not have an example entry for <code>contextStorage</code>.
If that is the case, you can copy the example above and add it yourself.</div>

#### Using multiple context stores

It is possible to configure more than one store so that some values are saved to
the local file-system and some are only held in memory.

For example, to configure the default store to be in-memory only, and a second
store for the file-system, the following options can be used:

```javascript
contextStorage: {
   default: "memoryOnly",
   memoryOnly: { module: 'memory' },
   file: { module: 'localfilesystem' }
}
```

In this example, the `default` property tells Node-RED which store to use if a
request to access context doesn't specify a store.

<div class="doc-callout"><em>Note</em> : if you choose to configure multiple
<code>localfilesystem</code> stores, you <em>must</em> set their <code>dir</code>
option so they use different directories to store data. Details on how to configure
the store is available <a href="/docs/api/context/store/localfilesystem#options">here</a></div>

Full details on the built-in modules, what configuration options they provide and
how to create custom modules, are available on the [api pages](../api/context/).

### フローでコンテキストを利用する

コンテキストに値を設定する最も簡単な方法は、Changeノードを使用することです。
例えば、以下のようなChangeノードのルールによって、
`msg.payload`の値を`flow`コンテキストに`myData`というキーで保管します。

<div style="text-align: center"><img src="/docs/user-guide/images/context_change.png" width="488px"></div>

様々なノードで直接コンテキストにアクセスすることができます。
例えば、Injectノードはコンテキスト値を注入するように設定することができ、
Switchノードはコンテキストに保管された値をもとにメッセージをルーティングすることができます。

If you have multiple context stores configured, the UI will allow you to pick
which store a value should be stored in.

<div style="text-align: center"><img src="/docs/user-guide/images/context_change_multiple_stores.png" width="471px"></div>


### Functionノードでコンテキストを利用する

[Functionノードの書き方ガイド](../writing-functions#storing-data)に、
Functionノードでのコンテキストの使い方が記述されています。

### カスタムノードでコンテキストを利用する

[ノードの開発ガイド](/docs/creating-nodes/context)にカスタムノードでのコンテキストの使い方が記述されています。
