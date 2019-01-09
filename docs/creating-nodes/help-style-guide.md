---
layout: docs
toc: creating-nodes-toc.html
title: ノードヘルプのスタイルガイド
---

ノードが選択されると、ヘルプが情報タブに表示されます。
ここにはユーザーにとって必要となるすべての情報が表示されるようにします。

以下に示すスタイルガイドを参考にすることにより、ノードをまたがってヘルプが統一された見た目や構成となるようにします。

<hr/>

<link href="/css/node-help.css" rel="stylesheet">

<div class="grid" style="min-height:auto; padding:5px 0 5px; border-bottom: 1px solid #f0f0f0;">
    <div class="col-1-2">
        このセクションはノードの概要を提供し、2から3程度の <code>&lt;p&gt;</code> で構成されます。
        最初の <code>&lt;p&gt;</code> はノードパレットでマウスをホバーした際にツールチップとして表示されます。
    </div>
    <div class="col-1-2 node-help" style="padding-right: 5px; background: #f9f9f9;">
        <p>Connects to a MQTT broker and publishes messages.</p>
    </div>
</div>
<div class="grid" style="min-height:auto; padding:5px 0 5px; border-bottom: 1px solid #f0f0f0;">
    <div class="col-1-2">
        ノードが入力をもつ場合、Inputsセクションではノードが使用するメッセージのプロパティについて説明します。
        また、各プロパティが期待する型についても記載します。
        ここでの説明は要約として、より詳細な内容がある場合は詳細セクションに記述します。
    </div>
    <div class="col-1-2 node-help" style="padding-right: 5px; background: #f9f9f9;">
        <h3>Inputs</h3>
            <dl class="message-properties">
                <dt>payload
                    <span class="property-type">string | buffer</span>
                </dt>
                <dd>the payload of the message to publish.</dd>
                <dt class="optional">topic <span class="property-type">string</span></dt>
                <dd>the MQTT topic to publish to.</dd>
            </dl>
    </div>
</div>
<div class="grid" style="min-height:auto; padding:5px 0 5px; border-bottom: 1px solid #f0f0f0;">
    <div class="col-1-2">
        ノードが出力をもつ場合もInputsセクションと同様に、どのようなプロパティのメッセージが出力されるか記載します。
        複数の出力がある場合は、その内容をリストとして列挙し、それぞれの内容について記述します。
    </div>
    <div class="col-1-2 node-help" style="padding-right: 5px; background: #f9f9f9;">
        <h3>Outputs</h3>
            <ol class="node-ports">
                <li>Standard output
                    <dl class="message-properties">
                        <dt>payload <span class="property-type">string</span></dt>
                        <dd>the standard output of the command.</dd>
                    </dl>
                </li>
                <li>Standard error
                    <dl class="message-properties">
                        <dt>payload <span class="property-type">string</span></dt>
                        <dd>the standard error of the command.</dd>
                    </dl>
                </li>
            </ol>
    </div>
</div>
<div class="grid" style="min-height:auto; padding:5px 0 5px; border-bottom: 1px solid #f0f0f0;">
    <div class="col-1-2">
        <p>Detailsセクションは、より詳細なノードの内容について記載します。
        どのように使われるべきか、入出力併せて記述するべきです。</p>
    </div>
    <div class="col-1-2 node-help" style="padding-right: 5px; background: #f9f9f9;">
        <h3>Details</h3>
            <p><code>msg.payload</code> is used as the payload of the published message.
            If it contains an Object it will be converted to a JSON string before being sent.
            If it contains a binary Buffer the message will be published as-is.</p>
            <p>The topic used can be configured in the node or, if left blank, can be set by <code>msg.topic</code>.</p>
            <p>Likewise the QoS and retain values can be configured in the node or, if left
            blank, set by <code>msg.qos</code> and <code>msg.retain</code> respectively.</p>
    </div>
</div>
<div class="grid" style="min-height:auto; padding:5px 0 5px;">
    <div class="col-1-2">
        <p>Referencesセクションは下記のような外部リンクなどを提供するために使用します。</p>
        <ul>
            <li>any relevant additional documentation. Such as how the Template node links
            to the Mustache language guide.</li>
            <li>the node's git repository or npm page - where the user can get additional help</li>
        </ul>
    </div>
    <div class="col-1-2 node-help" style="padding-right: 5px; background: #f9f9f9;">
        <h3>References</h3>
            <ul>
                <li><a>Twitter API docs</a> - full description of <code>msg.tweet</code> property</li>
                <li><a>GitHub</a> - the node's github repository</li>
            </ul>
    </div>
</div>

<hr/>

上記の例をHTMLで実装すると下記のようになります。

~~~~~html
<script type="text/x-red" data-help-name="node-type">
<p>Connects to a MQTT broker and publishes messages.</p>

<h3>Inputs</h3>
    <dl class="message-properties">
        <dt>payload
            <span class="property-type">string | buffer</span>
        </dt>
        <dd> the payload of the message to publish. </dd>
        <dt class="optional">topic <span class="property-type">string</span></dt>
        <dd> the MQTT topic to publish to.</dd>
    </dl>

<h3>Outputs</h3>
    <ol class="node-ports">
        <li>Standard output
            <dl class="message-properties">
                <dt>payload <span class="property-type">string</span></dt>
                <dd>the standard output of the command.</dd>
            </dl>
        </li>
        <li>Standard error
            <dl class="message-properties">
                <dt>payload <span class="property-type">string</span></dt>
                <dd>the standard error of the command.</dd>
            </dl>
        </li>
    </ol>

<h3>Details</h3>
    <p><code>msg.payload</code> is used as the payload of the published message.
    If it contains an Object it will be converted to a JSON string before being sent.
    If it contains a binary Buffer the message will be published as-is.</p>
    <p>The topic used can be configured in the node or, if left blank, can be set
    by <code>msg.topic</code>.</p>
    <p>Likewise the QoS and retain values can be configured in the node or, if left
    blank, set by <code>msg.qos</code> and <code>msg.retain</code> respectively.</p>

<h3>References</h3>
    <ul>
        <li><a>Twitter API docs</a> - full description of <code>msg.tweet</code> property</li>
        <li><a>GitHub</a> - the nodes github repository</li>
    </ul>
</script>
~~~~~


#### セクションヘッダー

各セクションは `<h3>` タグでマークアップします。
`Details` セクションがサブヘッダーとして必要な場合は、 `<h4>` タグを使用します。

~~~~~html
<h3>Inputs</h3>
...
<h3>Details</h3>
...
  <h4>A sub section</h4>
  ...
~~~~~


#### メッセージのプロパティ

メッセージのプロパティは `<dl>` を使用してリストにします。
リストには `message-properties` をクラスに指定します。

リスト内の各アイテムは `<dt>` と `<dd>` タグのペアとなるようにします。

それぞれの `<dt>` にはプロパティ名を記載し、さらに任意で `<span class="property-type">` 内にプロパティが期待する型について記載します。
プロパティが任意の場合は `<dt>` のクラスに `optional` を指定します。

それぞれの `<dd>` にはプロパティの要約を記述します。

~~~~~html
<dl class="message-properties">
    <dt>payload
        <span class="property-type">string | buffer</span>
    </dt>
    <dd> the payload of the message to publish. </dd>
    <dt class="optional">topic
        <span class="property-type">string</span>
    </dt>
    <dd> the MQTT topic to publish to.</dd>
</dl>
~~~~~


#### 複数出力

ノードに複数の出力がある場合、上記のように、それぞれの出力がメッセージのプロパティのリストを持つよう記述します。
これらのリストは `<ol>` でラップされクラスに `node-ports` を指定します。

リスト内の各アイテムの構成は、出力内容の簡単な説明を `<dl>` によるメッセージのプロパティーのリストで表現します。

<b>注意</b>: ノードの出力がひとつの場合は、このようなリストのラップは行わず単純に `<dl>` を使用します。

~~~~~html
<ol class="node-ports">
    <li>Standard output
        <dl class="message-properties">
            <dt>payload <span class="property-type">string</span></dt>
            <dd>the standard output of the command.</dd>
        </dl>
    </li>
    <li>Standard error
        <dl class="message-properties">
            <dt>payload <span class="property-type">string</span></dt>
            <dd>the standard error of the command.</dd>
        </dl>
    </li>
</ol>
~~~~~


#### 全般的なガイダンス

上記のように、メッセージのプロパティーをメッセージプロパティーの外部から参照する場合、利用者にそれを明確に伝えるため `msg.` を接頭辞として付けます。

~~~~~html
The interesting part is in <code>msg.payload</code>.
~~~~~

ヘルプテキスト内では `<b>`,`<i>` のような他のタグでマークアップしないようにします。

ノードのヘルプテキストは、利用者が経験豊富であったりNodeに精通していることを前提としてはいけません。
何よりもヘルプは利用者にとって役立つ必要があります。
