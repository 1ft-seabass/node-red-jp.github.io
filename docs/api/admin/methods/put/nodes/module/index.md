---
layout: docs
toc: api-toc.html
title: PUT /nodes/:module
---

ノードの有効・無効を設定します。

必要となる権限: <code>nodes.write</code>

### Headers

Header          | Value
----------------|-------
`Authorization` | `Bearer [token]` - 認証が有効になっている場合
`Content-type`  | `application/json`


### Arguments

Path Component | Description
---------------|------------
`module`       | モジュール名

リクエストボディは下記のようなJSON文字列でなければなりません:

Field     | Description
----------|------------------------
`enabled` | `true` または `false` - モジュールを有効にするか無効にするか

{% highlight json %}
{
  "enabled": true
}
{% endhighlight %}

### Response

Status Code | Reason           | Response
------------|------------------|--------------
`200`       | 成功             | [Node Module](/docs/api/admin/types#node-module) オブジェクト。レスポンスボディの例を参照
`400`       | 不正なリクエスト | [エラーを返す](/docs/api/admin/errors)
`401`       | 認証されなかった | _無し_
`404`       | 見つからなかった | _無し_


{% highlight json %}
{
  "name": "node-red-node-suncalc",
  "version": "0.0.6",
  "nodes": [
    {
      "id": "node-red-node-suncalc/suncalc",
      "name": "suncalc",
      "types": [
        "sunrise"
      ],
      "enabled": true,
      "loaded": true,
      "module": "node-red-node-suncalc"
    }
  ]
}
{% endhighlight %}
