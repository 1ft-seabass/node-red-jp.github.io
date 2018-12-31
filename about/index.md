---
layout: aboutSingle
title: 概要
---

Node-REDはフローベースドプログラミング(flow-based programming)ツールであり、
[IBM Emerging Technology Services](https://emerging-technology.co.uk)チームによって元は開発され、
現在では[JS Foundation](https://js.foundation)のひとつになっています。

## フローベースドプログラミング

1970年代にJ. Paul Morrisonによって発明された[flow-based programming](https://en.wikipedia.org/wiki/Flow-based_programming)は
アプリケーションの動作をブラックボックス、すなわちNode-REDでは「Node」のネットワークによって表す手法です。
各Nodeは、何らかのデータを与えられ、そのデータで何かを実行し、そのデータを渡すといった明確に定義された目的を持ちます。
ネットワークはNode間のデータの流れを示します。

これは視覚的な表現との相性が非常に良く、より幅広い範囲のユーザーにとってより利用しやすいモデルです。
誰かが問題を別々のステップに分解することができれば、Flowを見ることでそれが何をしているかがわかります、
つまり各Nodeの個々のコードについて理解する必要はありません。

## 実行環境/エディタ

Node-REDは、Flowエディタにアクセスするためにウェブブラウザを指定するNode.js上の実行環境に構築されています。
ブラウザでは、パレットからワークスペースへとNodeをドラッグし、自分のアプリケーションを作成します。
そしてシングルクリックで、アプリケーションを実行状態にある実行環境へとデプロイします。

Nodeのパレットは、コミュニティによって開発された新しいNodeをインストールすることで簡単に拡張することができ、
作成したFlowはJSONファイルによって簡単に共有することができます。


## 歴史

Node-REDは、2013年初頭にIBMのEmerging Technology ServicesグループのNick O'LearyとDave Conway-Jonesによる
サイドプロジェクトとして誕生しました。

MQTTトピック間のマッピングを視覚化および操作するための概念実証として始まったものは、
すぐにあらゆる面に簡単に拡張可能なはるかに汎用的なツールとなりました。

2013年9月にオープンソース化されて以来オープンで開発され、
2016年10月にJS Foundationの創設プロジェクトの1つとなったことで最高潮に達しました。


歴史といくつかのハイライト:

- [JS Foundationへの移行](http://nodered.org/blog/2016/10/17/js-foundation)についての記事を読む
- Nickの[Monki Gras 2016](https://www.youtube.com/watch?v=Bbg1017amZs)の講演を見る :

<div style="text-align: center">
    <iframe width="560" height="315" src="https://www.youtube.com/embed/Bbg1017amZs" frameborder="0" allowfullscreen></iframe>
</div>
