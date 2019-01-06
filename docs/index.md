---
layout: default
title: ドキュメント
---
## 概要

Node-REDドキュメント

### Getting Started

わずか数分でNode-REDをインストールして実行するためのガイドです。

- [インストール](getting-started/installation)
- [起動](getting-started/running)
- [Nodeの追加](getting-started/adding-nodes)
- [アップグレード](getting-started/upgrading)
- [はじめてのFlow作成](getting-started/first-flow)
- [2つ目のFlow作成](getting-started/second-flow)

Raspberry Piユーザは[こちら](hardware/raspberrypi)に直行してください。

### Node-REDの利用

Node-REDの設定と利用についてのガイドです。

- [設定](configuration)
- [セキュリティ](security)
- [Function Nodeの書き方](writing-functions)
- [既存アプリケーションへの組込み](embedding)

### プラットフォーム

Node-REDはエッジからクラウドまで様々なプラットフォーム上で実行することができます。
以下は各プラットフォームのスタートガイドです。

- [Raspberry Pi](hardware/raspberrypi)
- [BeagleBone Black](hardware/beagleboneblack)
- [Docker](platforms/docker)
- [IBM Bluemix](platforms/bluemix)
- [Amazon Web Services](platforms/aws)
- [Microsoft Azure](platforms/azure)


### Nodeの作成

独自のNodeを作成し、パレットに追加するためのガイドは[こちら](creating-nodes/)です。

### 質問がある場合

質問や提案をする（または単に挨拶したい）場合は
以下の様々な方法があります。

 - [メーリングリスト](https://groups.google.com/forum/#!forum/node-red)を購読
   したり[ブログ](http://blog.nodered.org)を確認する
 - [Slack](http://nodered.org/slack/)に参加する
 - [@nodered](http://twitter.com/nodered)をフォローする

バグや不具合を報告する際は
使用しているオペレーティングシステムとNode.jsとNode-REDのバージョンについての情報を含めるようにしてください。
これらはNode-REDの起動ログで確認できます。

    01 Jan 12:00:00 - [info] Node-RED version: v0.13.3
    01 Jan 12:00:00 - [info] Node.js  version: v4.2.6
