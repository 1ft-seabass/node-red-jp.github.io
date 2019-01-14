---
layout: docs
toc: creating-nodes-toc.html
title: HTMLファイル
---

`.html`ファイルは、エディタでのノード表示方法を定義します。
３つの種類があり、それぞれ独自の`<script>`タグで囲まれています:

1. エディタに登録されるメインノード定義。
   ここには定義するパレットカテゴリ、編集可能なプロパティ('defaults')、どのアイコンを使用するか。
   javascriptスクリプトタグ内に記述します。

2. ノードの編集ダイアログの内容を定義するテンプレート。
   これは `text-x-red`型のスクリプトで` data-template-name`がセットされて定義されています
   [ノードの種類]（＃node-type）に設定します。

3. サイドバータブに表示されるヘルプテキスト。
   これは `data-help-name`が
   [ノードの種類]（＃node-type）に設定された` text / x-red`型のスクリプトで定義されます。



### ノードの定義

ノードは`RED.nodes.registerType`を使用して
登録しなければなりません。

この関数はノードタイプと、その定義の2つの引数が必要です:

~~~~html
<script type="text/javascript">
    RED.nodes.registerType('node-type',{
        // node definition
    });
</script>
~~~~

#### ノードタイプ

ノードタイプはノードを識別するためエディタ全体で使用します。
対応する`.js`ファイルの`RED.nodes.registerType`への呼び出しで使用される値と
一致しなければなりません。

このタイプは、パレット内のノードのラベルとしても使用されます。
タイプが"in"または"out"で終わる場合、これはラベルから外されます。
例えば、"mqtt in"ノードと"mqtt out"ノードの両方が"mqtt"とラベル付けされ、
入力ポートと出力ポートの表示が"in"または"out"コンテキストを提供します。

#### ノード定義

ノード定義には、エディタに必要なノードに関するすべての情報が含まれています。
これは次のプロパティを持つオブジェクトです:


- `category`: (string) ノードが表示されるパレットカテゴリ
- `defaults`: (object) ノードの[プロパティの編集](properties)
- `credentials`: (object) ノードの[認証プロパティ](credentials)
- `inputs`: (number) ノードがいくつの入力を有するか `0` または `1`のどちらか.
- `outputs`: (number) ノードがいくつの出力を有するか `0`またはそれ以上にすることができる
- `color`: (string) 使用する[背景色](appearance#背景色)
- `paletteLabel`: (string\|function) パレットで使用する[ラベル](appearance#ラベル)
- `label`: (string\|function) ワークスペースで使用する[ラベル](appearance#ラベル)
- `labelStyle`: (string\|function) ラベルに適用する [スタイル](appearance#ラベルスタイル)
- `inputLabels`: (string\|function) ホバー上にノードの入力ポートに追加するオプションの [ラベル](appearance#port-labels)
- `outputLabels`: (string\|function) ホバー上にノードの出力ポートに追加するオプションの [ラベル](appearance#port-labels)
- `icon`: (string) 使用する[アイコン](appearance#アイコン)
- `align`: (string) アイコンとラベルの[位置合わせ](appearance#位置合わせ)
- `oneditprepare`: (function) 編集ダイアログが作成されている時に呼び出されます。 [カスタム編集の動作](properties#カスタム編集の動作)を参照してください。
- `oneditsave`: (function) 編集ダイアログがOKの時に呼び出されます。 [カスタム編集の動作](properties#カスタム編集の動作)を参照してください。
- `oneditcancel`: (function) 編集ダイアログがキャンセルの時に呼び出されます。[カスタム編集の動作](properties#カスタム編集の動作)を参照してください。
- `oneditdelete`: (function) 設定ノードの編集ダイアログの削除ボタンが押された時に呼び出されます。 [カスタム編集の動作](properties#カスタム編集の動作)を参照してください。
- `oneditresize`: (function) 編集ダイアログがリサイズされた時に呼び出されます [カスタム編集の動作](properties#カスタム編集の動作)を参照してください。
- `onpaletteadd`: (function) ノードタイプがパレットに追加された時に呼び出されます。
- `onpaletteremove`: (function) ノードタイプがパレットから削除されたときに呼び出されます。

### ダイアログの編集

ノードの編集テンプレートは編集ダイアログの内容を記述します。


    <script type="text/x-red" data-template-name="node-type">
        <div class="form-row">
            <label for="node-input-name"><i class="fa fa-tag"></i> Name</label>
            <input type="text" id="node-input-name" placeholder="Name">
        </div>
        <div class="form-tips"><b>Tip:</b> This is here to help.</div>
    </script>

いくつかの簡単な慣習があります:

 - ダイアログの各行をレイアウトするには、
   `form-row`クラスの`<div>`を使用する必要があります。
 - 典型的な行では、アイコンを含む`<label>`と、プロパティ名の後ろに`<input>`が続きます。
   アイコンは、[Font Awesome 4.7](https://fontawesome.com/v4.7.0/icons/)から
   入手可能なアイコンフォントから取得したクラスを持つ`<i>`要素を使用して作成されます。
 - もしインタラクティブ性が必要な場合は`oneditprepare`を使用して、
   ダイアログ要素に任意のイベントハンドラをアタッチすることができます。


編集テンプレートの使用方法の詳細については、
[こちら](properties#プロパティ編集ダイアログ)をご覧ください。


### ヘルプテキスト

ノードが選択されると、ヘルプテキストが情報タブに表示されます。
選択されたノードが何をするか意味と説明を表示する必要があります。
ここには送信時と、受信時に、
どのプロパティを設定できるか明確にする必要があります。

最初の `<p>`タグの内容は、パレット内のノードをホバリングするときにツールチップとして使用されます。

~~~~html
<script type="text/x-red" data-help-name="node-type">
   <p>Some useful help text to introduce the node.</p>
   <h3>Outputs</h3>
       <dl class="message-properties">
       <dt>payload
           <span class="property-type">string | buffer</span>
       </dt>
   <h3>Details</h3>
   <p>Some more information about the node.</p>
</script>
~~~~

ノードヘルプの完全なスタイルガイドが[ここにあります](help-style-guide)。
