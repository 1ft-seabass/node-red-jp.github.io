---
layout: default
title: 認証
---

デフォルトのNode-REDエディタはセキュアではありません。実行環境のIPアドレスとポートにアクセス可能な誰もがエディタにアクセスしてFlowを変更することができます。この方法は信頼できるネットワーク上で実行されている場合のみ適しています。

<div class="doc-callout">
<em>Note</em>: Node-REDの以前のリリースでは、設定で <code>httpAdminAuth</code> を有効にするとエディタにHTTPベーシック認証を使用することができました。この設定はまだ利用できますが推奨されず、今後は以下の <code>adminAuth</code> 設定が推奨されています。
</div>

### ユーザ認証の有効化

ユーザ認証を有効にするには以下のように `settings.js` を編集します。

{% highlight javascript %}
adminAuth: {
    type: "credentials",
    users: [{
        username: "admin",
        password: "$2a$08$zZWtXTja0fB1pzD4sHCMyOCMYz2Z6dNbM6tl8sJogENOMcxWV9DN.",
        permissions: "*"
    }]
}
{% endhighlight %}

`users` プロパティはユーザオブジェクトの配列です。これを使うと複数のユーザーが異なるアクセス許可を持つような設定が可能です。

例えば、上記設定例ではエディタ内で全ての権限を持ち `password` というパスワードの `admin` というユーザーを定義しています。パスワードは安全性を高めるためにbcryptアルゴリズムを使用してハッシュ化されていることに注意してください。

#### パスワードハッシュ値の生成方法

Node-REDインストールディレクトリ内で次のコマンドを実行することでパスワードハッシュ値を生成することができます。

    node -e "console.log(require('bcryptjs').hashSync(process.argv[1], 8));" your-password-here

その後、このコマンドの結果文字列をコピーして設定ファイルに貼り付けます。

#### デフォルトユーザの設定

上記の設定例ではログインしない限りエディタにアクセスできません。

しかし、ユーザがログインしなくても任意のアクセスレベルを提供したい場合もあります。例えば編集者への読み取り専用アクセス権の付与などです。これを行うには `adminAuth` 設定の `default` プロパティを以下のように設定します。

{% highlight javascript %}
adminAuth: {
    type: "credentials",
    users: [ /* list of users */ ],
    default: {
        permissions: "read"
    }
}
{% endhighlight %}

#### ユーザ権限

現在のリリースではユーザーは以下の2つの権限のうち、いずれかを持つことができます。

 - `*` - フルアクセス
 - `read` - 読み取り専用

#### トークン有効期限

アクセストークンのデフォルトの有効期限は7日間です。

有効期限は設定ファイルの `adminAuth` の `sessionExpiryTime` プロパティを変更することでカスタマイズすることができます。トークンの有効期間は秒単位で定義します。例えば1日後にトークンの有効期限が切れるようにするには以下のように設定します。

{% highlight javascript %}
adminAuth: {
    sessionExpiryTime: 86400,
    ...
}
{% endhighlight %}

### カスタムユーザ認証

設定ファイルにユーザ情報をハードコーディングせずにユーザー認証することも可能です。これを利用すれば既存の認証システムと統合することができます。

次の例ではカスタム認証コードを提供する外部モジュールを使用する方法を示しています。

1. `<node-red>/user-authentication.js` を以下のように作成します。

{% highlight javascript %}
var when = require("when");
module.exports = {
   type: "credentials",
   users: function(username) {
       return when.promise(function(resolve) {
           // ここでユーザ名が存在するかどうかチェックする処理を行う
           if (valid) {
               // ユーザ名が存在すればユーザオブジェクトを返す（ 'username' と 'permissions' プロパティは必須）
               var user = { username: "admin", permissions: "*" };
               resolve(user);
           } else {
               // ユーザ名が存在しない場合は 'null' を返す
               resolve(null);
           }
       });
   },
   authenticate: function(username,password) {
       return when.promise(function(resolve) {
           // ここでユーザー名/パスワードの組み合わせをチェックする処理を行う
           if (valid) {
               // ユーザー名/パスワードの組み合わせが有効な場合はユーザオブジェクトを返す
               var user = { username: "admin", permissions: "*" };
               resolve(user);
           } else {
               // ユーザー名/パスワードの組み合わせが無効な場合は 'null' を返す
               resolve(null);
           }
       });
   },
   default: function() {
       return when.promise(function(resolve) {
           // デフォルトユーザのユーザオブジェクトを返す
           resolve({anonymous: true, permissions:"read"});
       });
   }
}
{% endhighlight %}

2. 設定ファイルの `adminAuth` プロパティにパスを設定することで利用可能になります。

{% highlight javascript %}
adminAuth: require("./user-authentication");
{% endhighlight %}
