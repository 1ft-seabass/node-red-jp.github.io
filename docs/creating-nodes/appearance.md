---
layout: docs
toc: creating-nodes-toc.html
title: Node appearance
---

ノードの見た目として、
アイコン、背景色、ラベルの3点を変更できます。

### アイコン

ノードのアイコンは、定義内のthe `icon` プロパティに指定します。

プロパティの値は、文字列または関数を設定できます。

プロパティの値が文字列の場合は、その文字列をアイコン名として扱います。

プロパティの値が関数の場合は、ノードが最初に読み込まれた時、またはノードが編集された後に評価されます。関数はアイコン名として使う文字列を返すようにしてください。
関数は、ワークスペース上のノード（`this` が参照するノードインスタンス）と、パレット上のノードの両方のアイコンを表示するために使用されます。
パレット上のノード向けの場合、 `this` は特定のノードインスタンスを参照しません。
関数は有効な値を返す *必要があります。*

                ...
                icon: "file.png",
                ...

用意されたアイコン、またはノードと一緒に提供されるアイコンを利用できます。

#### 用意されたアイコン

<style>
.nr-icon-list {
    background: #666;
}
.nr-icon-list li {
    width: 31%;
    display: inline-block;
    color: #fff;
    vertical-align: middle;
    margin: 10px 5px;
}
.nr-icon-list li>img  {
    vertical-align: middle;
    max-width: 20px;
    margin-right: 20px;
}
</style>

<ul class="nr-icon-list">
<li><img src="images/alert.png"/> alert.png</li>
<li><img src="images/arrow-in.png"/> arrow-in.png</li>
<li><img src="images/bridge-dash.png"/> bridge-dash.png</li>
<li><img src="images/bridge.png"/> bridge.png</li>
<li><img src="images/db.png"/> db.png</li>
<li><img src="images/debug.png"/> debug.png</li>
<li><img src="images/envelope.png"/> envelope.png</li>
<li><img src="images/feed.png"/> feed.png</li>
<li><img src="images/file.png"/> file.png</li>
<li><img src="images/function.png"/> function.png</li>
<li><img src="images/hash.png"/> hash.png</li>
<li><img src="images/inject.png"/> inject.png</li>
<li><img src="images/light.png"/> light.png</li>
<li><img src="images/serial.png"/> serial.png</li>
<li><img src="images/template.png"/> template.png</li>
<li><img src="images/white-globe.png"/> white-globe.png</li>
</ul>

#### 独自アイコン

ノードの `.js` ファイルや `.html` ファイルと同じディレクトリに存在する `icons` ディレクトリの中に、ノード固有の独自アイコンを配置します。
アイコンのファイル名を指定すると、エディタは本ディレクトリ内からアイコンを探し出します。
そのため、アイコンのファイル名は一意にする必要があります。

アイコンは、背景を透過色にした20 x 30 ピクセルの白い画像にしてください。

#### ユーザ定義アイコン

Node-RED 0.18版以降、ノードのアイコンは設定エディタの`node settings`を利用することで上書きできるようになりました。

<div style="text-align:center">
    <img title="port label editor" src="images/user-defined-icon.png"/>
</div>

1つもしくは複数のアイコンファイルを含むインストール済のモジュール名または`node-red`のコアノードの名称が左に表示されます。モジュールのアイコンファイルの名前が右に表示されます。

**Note**: ノードがデフォルトの`icon`プロパティを持っている場合、ノードのアイコンは上書きすることができません（例 ui_button node of node-red-dashboard）。

### 背景色

ノードの背景色は、異なるノードの型を素早く区別するための主な手段の1つです。
ノードの背景色は、ノード定義の `color` プロパティに定義してください。

{% highlight javascript %}
...
color: "#a6bbcf",
...
{% endhighlight %}

Node-REDでは落ち着いた色を採用しています。 
新たなノードは、この色に合うようにしてみてください。

以下は、ノードの背景色として良く使われる色です。:



<style>
.nr-color-list {
}
.nr-color-list li {
text-align: center;
    border: 1px solid #333;
    width: 31%;
    display: inline-block;
    color: #333;
    vertical-align: middle;
    padding: 10px 5px;
    margin-bottom: 5px;
}
</style>

<ul class="nr-color-list">
<li style="background: #3FADB5">#3FADB5</li>
<li style="background: #87A980">#87A980</li>
<li style="background: #A6BBCF">#A6BBCF</li>
<li style="background: #AAAA66">#AAAA66</li>
<li style="background: #C0C0C0">#C0C0C0</li>
<li style="background: #C0DEED">#C0DEED</li>
<li style="background: #C7E9C0">#C7E9C0</li>
<li style="background: #D7D7A0">#D7D7A0</li>
<li style="background: #D8BFD8">#D8BFD8</li>
<li style="background: #DAC4B4">#DAC4B4</li>
<li style="background: #DEB887">#DEB887</li>
<li style="background: #DEBD5C">#DEBD5C</li>
<li style="background: #E2D96E">#E2D96E</li>
<li style="background: #E6E0F8">#E6E0F8</li>
<li style="background: #E7E7AE">#E7E7AE</li>
<li style="background: #E9967A">#E9967A</li>
<li style="background: #F3B567">#F3B567</li>
<li style="background: #FDD0A2">#FDD0A2</li>
<li style="background: #FDF0C2">#FDF0C2</li>
<li style="background: #FFAAAA">#FFAAAA</li>
<li style="background: #FFCC66">#FFCC66</li>
<li style="background: #FFF0F0">#FFF0F0</li>
<li style="background: #FFFFFF">#FFFFFF</li>
</ul>


### ラベル

ノードのラベルについては、 `label` 、 `paletteLabel` 、 `outputLabel` 、 `inputLabel`　の4つのプロパティがあります。

#### ノードラベル

The `label` of a node in the workspace can either be a static piece of text, or
it can be set dynamically on a per-node basis according to the nodes properties.

The value of the property can be either a string or a function.

If the value is a string, that is used as the label.

If the value is a function, it will get evaluated when the node is first loaded,
or after it has been edited. The function is expected to return the value to use
as the label.

As mentioned in a previous section, there is a convention for nodes to have a
`name` property to help distinguish between them. The following example shows
how the `label` can be set to pick up the value of this property or default to
something sensible.

{% highlight javascript %}
...
label: function() {
    return this.name||"lower-case";
},
...
{% endhighlight %}

Note that it is not possible to use [credential](credentials) properties in the label function.

#### パレットラベル

標準では、ノードの型をパレット上のノード名として使います。
`paletteLabel` プロパティを指定すると、標準のノード名を上書きします。

As with `label`, this property can be either a string or a function. If it is a
function, it is evaluated once when the node is added to the palette.

#### Label style

The css style of the label can also be set dynamically, using the `labelStyle`
property. Currently, this property must identify the css class to apply. If
not specified, it will use the default `node_label` class. The only other
predefined class is `node_label_italic`.

<div style="text-align: center">
    <img title="node label style" src="images/node_label_style.png"/>
</div>

The following example shows how `labelStyle` can be set to `node_label_italic`
if the `name` property has been set:

{% highlight javascript %}
...
labelStyle: function() {
    return this.name?"node_label_italic":"";
},
...
{% endhighlight %}

#### Alignment

By default, the icon and label are left-aligned in the node. For nodes that sit
at the end of a flow, the convention is to right-align the content. This is done
by setting the `align` property in the node definition to `right`:

{% highlight javascript %}
...
align: 'right',
...
{% endhighlight %}

<div style="text-align: center">
    <img title="node label alignment" src="images/node_alignment.png"/>
</div>


#### Port labels

From Node-RED version **0.17** onwards nodes can optionally provide labels on their input
and output ports, that can be seen by hovering the mouse over the port.

<div style="text-align:center">
    <img title="port labels" src="images/node-labels.png"/>
</div>

These can either be set statically by the node's html file

    ...,
    inputLabels: "parameter for input",
    outputLabels: ["stdout","stderr","rc"],
    ...

or generated by a function, that is passed an index to indicate the output pin (starting from 0).

    ...,
    outputLabels: function(index) {
        return "my port number "+index;
    }
    ...


In both cases they can be overwritten by the user using the `node settings` section of the configuration editor.

<div style="text-align:center">
    <img title="port label editor" src="images/node-labels-override.png"/>
</div>

<div style="text-align:center">
    <img title="port custom labels" src="images/node-labels-custom.png"/>
</div>

**Note**: Labels are not generated dynamically, and cannot be set by `msg` properties.
