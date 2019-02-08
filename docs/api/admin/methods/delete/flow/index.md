---
layout: docs
toc: api-toc.html
title: DELETE /flow/:id
---

個別のフロー設定を削除します。

必要となる権限: <code>flows.write</code>

### Headers

Header          | Value
----------------|-------
`Authorization` | `Bearer [token]` - 認証が有効になっている場合

### Arguments

Path Component | Description
---------------|------------
`id`           | フローのid

### Response

Status Code | Reason           | Response
------------|------------------|--------------
`204`       | 成功             | _無し_
`400`       | 不正なリクエスト | [エラーを返す](/docs/api/admin/errors)
`401`       | 認証されなかった | _無し_
`404`       | 見つからなかった | _無し_
