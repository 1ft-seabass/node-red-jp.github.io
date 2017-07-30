---
layout: default
toc: creating-nodes-toc.html
title: Node context
---

ノードは、そのコンテキストオブジェクト内にデータを格納することができます。このコンテキストオブジェクトは、ノードの再デプロイ時およびNode-REDの再起動時にリセットされます。

{% highlight javascript %}
// Access the node's context object
var context = this.context();

var count = context.get('count') || 0;
count += 1;
context.set('count',count);

{% endhighlight %}

##### フローコンテキスト

フローレベルのコンテキストは、特定のタブ上のすべてのノードによって共有されます。

{% highlight javascript %}
var flowContext = this.context().flow;
var count = flowContext.get('count')||0;
{% endhighlight %}

##### グローバルコンテキスト

グローバルコンテキストは、すべてのノードによって共有され、すべてのノードにアクセス可能です。たとえば、変数`foo`をグローバルに利用できるようにするには:

{% highlight javascript %}
var globalContext = this.context().global;
globalContext.set("foo","bar");  // this is now available to other nodes
{% endhighlight %}

##### 関数ノードのコンテキストへのアクセス

関数ノードでは、`flow`コンテキストオブジェクトと`global`コンテキストオブジェクトがトップレベルオブジェクトとして利用可能になります。詳細については、[このセクション](/docs/writing-functions#storing-data)を参照してください。
