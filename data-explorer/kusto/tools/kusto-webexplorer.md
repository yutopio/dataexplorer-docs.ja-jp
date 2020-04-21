---
title: クスト.ウェブエクスプローラ - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの Kusto.WebExplorer について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 1f6926df09a207cfea2b9201ef57f36932a63f74
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523870"
---
# <a name="kustowebexplorer"></a>クスト.ウェブエクスプローラ

Kusto.WebExplorer は、Kusto サービスにクエリおよび制御コマンドを送信するために使用できる Web アプリケーションです。 アプリケーションは[ ]https://dataexplorer.azure.com/でホストされ、[ ]https://aka.ms/kweによってショートリンクされます。



また、HTML IFRAME で他の Web ポータルでホストすることもできます。
(たとえば、これは[Azure ポータル](https://portal.azure.com)で行われます)。それをホストする方法とそれが使用するモナコエディタの詳細については、モナコ[IDE](../api/monaco/monaco-kusto.md)を参照してください。

## <a name="connect-to-multiple-clusters"></a>複数のクラスターに接続する

複数のクラスターを接続し、データベースとクラスターを切り替えることができるようになりました。
このツールは、接続先のクラスターとデータベースを簡単に識別するように設計されています。

![alt text](./Images/KustoTools-WebExplorer/AddingCluster.gif "クラスタの追加")

## <a name="recall-results"></a>リコール結果

多くの場合、分析中に複数のクエリを実行し、前のクエリの結果を再検討する必要があります。 この機能を使用すると、クエリを再実行しなくても結果を呼び出すことができます。 データは、ローカルのクライアント側キャッシュから提供されます。

![alt text](./Images/KustoTools-WebExplorer/RecallResults.gif "リコール結果")

## <a name="enhanced-results-grid-control"></a>拡張結果グリッドコントロール

テーブル グリッドでは、複数の行、列、およびセルを選択できます。 複数のセル (Excel など) を選択して集計を計算し、データをピボットします。

![alt text](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "強化されたグリッド")

## <a name="intellisense--formatting"></a>インテリセンス&書式設定

"Shift + Alt + F" ショートカット キー、コード折りたたみ (アウトライン)、および IntelliSense を使用して、きれいに印刷形式を使用できます。

![alt text](./Images/KustoTools-WebExplorer/Formating.gif "整形")

## <a name="deep-linking"></a>ディープリンク

ディープ リンクまたはディープ リンクとクエリのみをコピーできます。 次のテンプレートを使用して、クラスター、データベース、およびクエリを含むように URL を書式設定することもできます。

`https://dataexplorer.azure.com/`[`clusters/`*クラスタ*`/databases/` [*データベース*]`?`*オプション*]] ]

次のオプションを指定できます。

* `workspace=empty`: 新しい空のワークスペースを作成することを示します (以前のクラスター、タブ、およびクエリの呼び戻しは行われません)。



![alt text](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>フィードバック

このツールを使用してフィードバックを送信できます。
![alt text](./Images/KustoTools-WebExplorer/Feedback.gif "フィードバック")