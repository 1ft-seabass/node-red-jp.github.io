---
layout: default
title: Nodeの作成
---

パレットに新しいNodeを追加することでNode-REDを拡張することができます。Node作成方法について以下のセクションを参照ください。

 - [Node作成](first-node.html)
 - [JavaScriptファイル](node-js.html)
 - [HTMLファイル](node-html.html)
 - [コンテキスト](context.html)
 - [プロパティ](properties.html)
 - [クレデンシャル](credentials.html)
 - [UI](appearance.html)
 - [ステータス](status.html)
 - [設定](config-nodes.html)
 - [パッケージング](packaging.html)
 - [国際化](i18n.html)

### ガイドライン

新しいNodeを作成する際に従うべき幾つかの一般的な原則があります。これらは標準Nodeのアプローチに従って一貫したユーザー体験を提供するのに役立ちます。

- **Nodeの目的は明確に定義されるべきです**

   APIのすべての可能なオプションを公開したNodeは、潜在的に、各Nodeのグループは、単一の目的を果たすことはあまり有用です。
   A node that exposes every possible option of an API is potentially less useful that a group of nodes that each serve a single purpose.

- **Nodeはどんな機能に関わらず使いやすく作りましょう**

   複雑さを隠蔽して専門用語やドメイン固有の知識の使用を避けます。

- **多様なメッセージ型が渡されても正常に処理するようにしましょう**

   メッセージは文字列、数値、Boolean、Buffer、オブジェクト、配列、nullなどの様々な型で渡される可能性があります。いずれの型であっても正しく処理される必要があります。

- **Nodeの送信には一貫性を持つべきです**

   Nodeがメッセージにどんなプロパティを付与するか一貫性を持ち且つ予測可能であり、それらをドキュメント化する必要があります。

- **Flowの先頭、中間、または末尾に座って - いないすべてを一度に。**
  sit at the beginning, middle or end of a flow - not all at once.

- **エラーをキャッチしましょう**

   NodeがエラーをスローしてもNode-RED全体は停止しません。Nodeは可能な限りエラーをキャッチし、作成したすべての非同期呼び出しのエラーハンドラを登録する必要があります。
