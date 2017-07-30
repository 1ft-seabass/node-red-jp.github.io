---
layout: default
toc: creating-nodes-toc.html
title: Node properties
---

ノードのプロパティは、html定義の`defaults`オブジェクトによって定義されます。
これらは、ノードのインスタンスがランタイムで作成された時にノードコンストラクタ関数に渡されるプロパティです。

[はじめてのノード開発](first-node)の例では、ノードには`name`という単一のプロパティがありました。このセクションでは、新しいノードに`prefix`という名前のプロパティを追加します:

1. `defaults`オブジェクトに新しいエントリを追加します:

        defaults: {
            name: {value:""},
            prefix: {value:""}
        },

   エントリには、このタイプの新しいノードがワークスペースにドラッグされたときに使用されるデフォルトの`value`が含まれます。

2. ノードの編集テンプレートにエントリを追加する

        <div class="form-row">
            <label for="node-input-prefix"><i class="icon-tag"></i> Prefix</label>
            <input type="text" id="node-input-prefix">
        </div>

    テンプレートには、`id`が`node-input-<propertyname>`に設定された`<input>`要素が含まれていなければなりません。

3. ノード内のプロパティを使用する

        function LowerCaseNode(config) {
            RED.nodes.createNode(this,config);
            this.prefix = config.prefix;
            var node = this;
            this.on('input', function(msg) {
                msg.payload = node.prefix + msg.payload.toLowerCase();
                node.send(msg);
            });
        }

### プロパティ定義

`defaults`の配列は以下の属性を持つことができます:

- `value` : (任意の型) プロパティが持つデフォルト値
- `required` : (boolean) *オプション* プロパティが必須かどうか。trueにすると、値がnullまたは空文字列の場合、プロパティが無効になります。
- `validate` : (function) *オプション* プロパティの値を検証するために使用できる関数。
- `type` : (string) *オプション* このプロパティが構成ノードへのポインターである場合、これはノードのタイプを識別します。

### 予約済みのプロパティ名

使用してはいけないプロパティの予約名がいくつかあります:

> `type`, `x`, `y`, `z`, `wires`, `outputs`


ノードが提供する出力の数を設定可能にしたい場合、`outputs`を`defaults`配列に含めることができます。Functionノードは、これがどのように機能するかの例です。

### プロパティ検証

エディターは、無効な値が与えられた場合にユーザに警告するために、すべてのプロパティを検証しようとします。

`required`属性は、プロパティが非nullで非blankでなければならないことを示すために使用でいます。

より具体的な検証が必要な場合、`validate`属性を使用して、値が有効であることを確認する関数を提供することができます。
関数には値が渡され、trueまたはfalseが返されます。
これは、ノードのコンテキスト内で呼び出され、ノードの他のプロパティにアクセスするために、`this`を使用できることを意味します。
これにより、検証は他のプロパティ値に依存することができます。

共通の検証関数が用意されています。

 - `RED.validators.number()` - 値が数字であることを確認する
 - `RED.validators.regex(re)` - 指定された正規表現に一致する値をチェックする


次の例では、これらのバリデータのそれぞれを適用することができる方法を示しています。

{% highlight javascript %}
defaults: {
   minimumLength: { value:0, validate:RED.validators.number() },
   lowerCaseOnly: {value:"", validate:RED.validators.regex(/[a-z]+/) },
   custom: { value:"", validate:function(v) { return v.length > this.minimumLength } }
},
{% endhighlight %}

`custom`プロパティは、その長さが`minimumLength`プロパティの値よりも大きい場合に有効です。


### プロパティ編集ダイアログ

編集ダイアログが開かれると、エディタはノードの編集テンプレートをダイアログに入力します。

`defaults`配列の各プロパティについて、`id`が`node-input-<propertyname>`に設定された`<input>`要素を探します。
この入力には、プロパティの現在の値が自動的に入力されます。
編集ダイアログが正常になると、プロパティは入力にある値を取ります。

`<input>`型は、文字列/数値プロパティの`text`か、booleanプロパティの`checkbox`です。あるいは、選択肢が限られている場合は、`<select>`要素を使用することができます。

#### カスタム編集の動作

デフォルトの動作は多くの場合動作しますが、場合によってはいくつかのノード固有の動作を定義します。
たとえば、プロパティを簡単な`<input>`や`<select>`として適切に編集できない場合や、編集ダイアログのコンテンツ自体に、選択されているオプションに基づいて特定の動作を持っている必要があります。

ノード定義には、編集動作をカスタマイズする2つの関数を含めることができます。

 - `oneditprepare`は編集ダイアログが表示される直前に呼び出されます。
 - `oneditsave`は編集ダイアログがOKの時に呼び出されます。
 - `oneditcancel`は編集ダイアログがキャンセルの時に呼び出されます。
 - `oneditdelete`は設定ノードの編集ダイアログの削除ボタンが押された時に呼び出されます。
 - `oneditresize`は編集ダイアログがリサイズされた時に呼び出されます。

例えば、注入ノードが繰り返すように構成されている場合、それはcronのような文字列として設定します: `1,2 * * * *`
ノードは、その文字列を解析してよりユーザフレンドリなUIを提示できる`oneditprepare`関数を定義します。
また、ユーザが選択したオプションを対応するcron文字列にコンパイルする`oneditsave`関数もあります。
