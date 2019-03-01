---
layout: aboutSingle
title: Node-REDへの貢献
---

いかなる貢献も出発点はコミュニティでのディスカッションです。
そのための望ましい過程はプロジェクトのメーリングリストですが、
Slackチームでのディスカッションも同様に妥当です。これらは次の2つの目的を果たしています。

 - GitHubユーザではない人々を含む幅広い支援者に届けること。
 - 実際には支援依頼または重複している項目を、
   除外や合理化して処理してから
   対応できるようにすること。

コミュニティでのディスカッションで結論が出た時点で、
リクエストは撤回されるか次の段階に進みます。

デザインまたはコードのコントリビュータの立場ではないコミュニティメンバからの機能要求については、
コミッタによって
[Trello Whiteboard](https://trello.com/b/R0O3CSrI/node-red-whiteboard)に追加されます。
このホワイトボードは
作業の優先順位付けの際に技術委員会が汲み上げる可能性のある機能のバックログとして機能しています。

If an item has a Contributor to do the work, the next step will depend on the nature
of the change.

If it is a well-defined feature that has limited impact - such as adding a new
option to an existing node - an issue should be raised in the appropriate
repository with the `feature` label applied. The issue should provide a
description of the feature and any key design points that are needed.
This issue can then be used to help refine the proposal. The issue should
clearly identify who is working on it - this is to avoid accidental duplication
of work and ensure there is a focal point for the work.

If it is a larger scoped feature that may require multiple pull-requests to
implement, or has a great impact to the end user, a design note should be
created in the [node-red/designs](https://github.com/node-red/designs) repo.

Once the design has been approved and moved to the `in-progress` state, an issue
should be raised in the appropriate repository with the `feature` label applied.
If the design identifies multiple stages of delivery for the feature, an issue
should be raised for each stage as needed. The issues should reference the design
note. Each issue should have a milestone set if it is planned for a particular release.

At some point a pull request will arrive. This will go through the normal review
processes. The Committer community have a responsibility to review the PR in a
timely manner. For any significant changes, the PR should be allowed to sit on
the list for at least 48 hours - with consideration for weekends and other
holiday periods. This gives all committers a fair chance to review the PR, or at
least register their desire to give it a proper review, regardless of their
timezone.

未解決の異議がなければ、コミッタはPRを承認します。

異議がある場合、コミッタ間で適切な合意に達しなれければなりません。

issueに関する全てのPRが解消された時点で、issueをクローズすることができます。

Once it has been merged, the corresponding feature issue should be
closed. If there is a corresponding design note, its history section should be
updated to reflect where/when the item was delivered.


## プルリクエストの要件

コードの変更をおこなったすべてのプルリクエストは、承認される前にいくつかの基準を満たしていることが求められます。

1. PRへのすべてのコミッタはJS Foundation CLAに同意しなくてはなりません。
   同意していないコミッタからの変更をプロジェクトは承認できません。

2. PRによって初めてプロジェクトがコントリビュートに気がつくべきではありません。
   前述のとおり、
   すべてのコードコントリビュートはコミュニティや
   事前に作成されているissueで既に議論されているべきです。
   
   PRがバグ修正を含んでいる場合、
   議論が必要な変更がないかぎり承認されます。

   重大な変更がある場合、適切なコントリビューションのプロセスに従っていなければ却下される可能性があります。
   私達はコントリビューションを思いとどまらせたいわけではなく、
   適切なレビュー/ディスカッションの過程を省略することを避けたいのです。

3. ビルドテストはきれいに合格するはずです。
   テストは各PRに対して自動実行され、報告されます。
   このテストは、単体テストおよび機能テストと同様にコードスタイルおよびリントを含んでいます。

4. 変更には適切なテストカバレッジが含まれている必要があります。
   コアプロジェクトは90%以上のコードカバレッジの維持を目標としています。
   PRがコードカバレッジを低下させる場合、正当な理由がないかぎり却下されます。

このプロジェクトでは、
PRをおこなう以前にコード変更のためにどの開発方法論を用いるかという要件は設けていません。
優れたテストカバレッジとドキュメントに対して価値を見出しています。

## Gitワークフロー

主なプロジェクトのリポジトリのGitでは以下のワークフローが利用されています。

従来のGitワークフローと同様に、異なる活動ごとに複数のブランチが使用されています。
以下に現在の利用用途について記載しています。

 - `master` - このブランチにはNode-REDの最新リリース版、およびそれに追加したバグ修正が含まれています。
   このブランチはいつでも次のメンテナンスリリースとしてリリースできるでしょう。
   このドキュメントを書いている時点では、
   ブランチには0.16.2と0.16.3として将来リリースされる予定のいくつかの修正が含まれています。
 - `dev` - this branch contains the development work on the next milestone release.
   This is where new features are developed.
 - より大きな機能はそれぞれ独自のブランチで開発され、
   機能が幅広い利用に対して
   十分に安定したときに開発ブランチにマージされます。

## ツール

プロジェクトではその活動を支援するため、数多くのツールが利用されています。
以下のリストは、現在利用されているツールとその目的を反映しています。


 - [フォーラム](https://discourse.nodered.org)

   This is the main discussion venue for the project. 誰でもメーリングリストに参加することができます。

 - [Slack](https://nodered.org/slack)

   For active, real-time, conversations, slack can be more productive than the
   forum. Anyone can join the slack team. The `#dev` channel is used for discussions
   on project development.

 - [Trello - Whiteboard](https://trello.com/b/R0O3CSrI/node-red-whiteboard)

   アイデアと機能を高度に追跡できます。
   既存の機能を拡張したり、
   より「偉大な」アイデアや機能をバックログに含めることができるツールです。

   このツールは技術委員会がアイデアや機能に優先順位を決定するために利用します。
   誰でもホワイトボードを閲覧することができます。コミッタは書き込み権限を有しています。

 - [Trello - Documentation](https://trello.com/b/m2mBMUYj/documentation)

   特定のコード変更に結び付けられていないドキュメント作成タスクのため、異なるホワイトボードが利用されています。
   ユーザ/開発者/コントリビュータそれぞれに適したドキュメントを提供できるようにしています。
   誰でもホワイトボードを閲覧することができます。
   コミッタは書き込み権限を有しています。

 - [GitHub - code](https://github.com/node-red/node-red)

   プロジェクトのソースコードのバージョン管理。
   誰でもリポジトリをクローンまたはフォークできます。
   プライベートリポジトリは存在しません。コミッタはリポジトリへのコミットを適用できます。

 - [GitHub - issues](https://github.com/node-red/node-red/issues)

   バグ/課題の報告および機能開発の追跡。
   誰でもissueを作成することができます。コミッタはissueを「所持」することができます。

 - [GitHub - pull requests](https://github.com/node-red/node-red/pulls)

   コントリビューションをリポジトリに貢献する方法。誰でもプルリクエストを出すことができます。
   そのためのプロセスは別途記載します。

 - [GitHub - wiki](https://github.com/node-red/node-red/wiki)

   それぞれのリポジトリは関連したwikiがあります。共同でデザイン、アイデアおよびドキュメンテーションを
   開発するための作業スペースとして利用されています。

   特に、コアリポジトリのwikiは一連の[Design Notes](https://github.com/node-red/node-red/wiki/Design-Notes)
   ページがあります。これはデザインが開発され、フィードバックが求められるスペースです。

   wikiにはアクセス権の制御がありません。誰でも変更をおこなうことができます。しかし、変更は
   ページに積極的に取り組んでいる人々との共同作業によってのみ実施されるべきです。