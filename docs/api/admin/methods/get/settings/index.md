---
layout: docs
toc: api-toc.html
title: GET /settings
---

ランタイムの設定を取得します。

必要となる権限: <code>settings.read</code>

### Headers

Header | Value
-------|-------
`Authorization` | `Bearer [token]` - 認証が有効になっている場合

### Response

Status Code | Reason           | Response
------------|------------------|--------------
`200`       | 成功             | 下記例を参照
`401`       | 認証されなかった | _無し_

{% highlight json %}
{
  "httpNodeRoot": "/",
  "version": "0.X.Y",
  "user": {
    "username": "admin",
    "permissions": "*"
  }
}
{% endhighlight %}

レスポンスオブジェクトは下記のフィールドを含みます:

Field          | Description
---------------|------------
`httpNodeRoot` | HTTPノードのルートパス
`version`      | Node-REDランタイムのバージョン
`user`         | ログインしているユーザー。このフィールドは[認証が有効になっている場合](/docs/security)含まれます。
