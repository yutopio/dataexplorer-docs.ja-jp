---
title: Kusto. WebExplorer-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Kusto. WebExplorer について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: d53f12c4a0c4dd2bce669dbe004b8f325db27af5
ms.sourcegitcommit: 4986354cc1ba25c584e4f3c7eac7b5ff499f0cf1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2020
ms.locfileid: "84856389"
---
# <a name="kustowebexplorer"></a>Kusto. WebExplorer

Kusto。 WebExplorer は、Kusto サービスにクエリおよび制御コマンドを送信するために使用できる web アプリケーションです。 アプリケーションはでホストされ、 https://dataexplorer.azure.com/ によって短時間でリンクされ https://aka.ms/kwe ます。



Kusto. WebExplorer は、HTML IFRAME 内の他の web ポータルでもホストできます。
(たとえば、これは[Azure portal](https://portal.azure.com)によって行われます)。これをホストする方法の詳細については、「[モナコ IDE](../api/monaco/monaco-kusto.md) 」を参照してください。

## <a name="connect-to-multiple-clusters"></a>複数のクラスターへの接続

複数のクラスターに接続し、データベースとクラスターを切り替えることができるようになりました。
このツールは、接続しているクラスターとデータベースを簡単に識別できるように設計されています。

![alt text](./Images/KustoTools-WebExplorer/AddingCluster.gif "クラスターのまたは")

## <a name="recall-results"></a>再呼び出しの結果

多くの場合、分析時に複数のクエリを実行し、前のクエリの結果を再表示する必要がある場合があります。 この機能を使用すると、クエリを再実行しなくても結果を思い出すことができます。 データは、ローカルのクライアント側キャッシュから提供されます。

![alt text](./Images/KustoTools-WebExplorer/RecallResults.gif "RecallResults")

## <a name="enhanced-results-grid-control"></a>強化された結果グリッドコントロール

テーブルグリッドでは、複数の行、列、およびセルを選択できます。 複数のセル (Excel など) を選択してデータをピボットすることによって集計を計算します。

![alt text](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Intellisense & の書式設定

"Shift + Alt + F" ショートカットキー、コードの折りたたみ (アウトライン)、および IntelliSense を使用して、非常に印刷形式を使用できます。

![alt text](./Images/KustoTools-WebExplorer/Formating.gif "フォーマット")

## <a name="deep-linking"></a>ディープリンク

ディープリンクまたはディープリンクだけをコピーすることも、クエリをコピーすることもできます。 次のテンプレートを使用して、クラスター、データベース、およびクエリを含めるように URL をフォーマットすることもできます。

`https://dataexplorer.azure.com/`[ `clusters/` *Cluster* [ `/databases/` *データベース*[ `?` *オプション*]]]

次のオプションを指定できます。

* `workspace=empty`: 新しい空のワークスペースを作成することを示します (以前のクラスター、タブ、およびクエリの再呼び出しは行われません)。



![alt text](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="feedback"></a>フィードバック

ツールを使用してフィードバックを送信できます。
![alt text](./Images/KustoTools-WebExplorer/Feedback.gif "フィードバック")
