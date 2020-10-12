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
ms.openlocfilehash: 70b25a1dbfcc3d5cba134875a63c2ac24a018277
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942286"
---
# <a name="kustowebexplorer"></a>Kusto. WebExplorer

Kusto。 WebExplorer は、Kusto サービスにクエリおよび制御コマンドを送信するために使用できる web アプリケーションです。 アプリケーションはでホストされ、 https://dataexplorer.azure.com/ によって短時間でリンクされ https://aka.ms/kwe ます。



Kusto. WebExplorer は、HTML IFRAME 内の他の web ポータルでもホストできます。
(たとえば、これは [Azure portal](https://portal.azure.com)によって行われます)。これをホストする方法の詳細については、「 [モナコ IDE](../api/monaco/monaco-kusto.md) 」を参照してください。

## <a name="connect-to-multiple-clusters"></a>複数のクラスターへの接続

複数のクラスターに接続し、データベースとクラスターを切り替えることができるようになりました。
このツールは、接続しているクラスターとデータベースを簡単に識別できるように設計されています。

![アニメーション GIF。Azure データエクスプローラーで [クラスターの追加] をクリックし、ダイアログボックスにクラスター名を入力すると、左側のウィンドウにクラスターが表示されます。](./Images/KustoTools-WebExplorer/AddingCluster.gif "クラスターのまたは")

## <a name="recall-results"></a>再呼び出しの結果

多くの場合、分析時に複数のクエリを実行し、前のクエリの結果を再表示する必要がある場合があります。 この機能を使用すると、クエリを再実行しなくても結果を思い出すことができます。 データは、ローカルのクライアント側キャッシュから提供されます。

![アニメーション GIF。2つの Azure データエクスプローラークエリを実行すると、マウスが最初のクエリに移動し、[リコール] がクリックされます。最初の結果が再び表示されます。](./Images/KustoTools-WebExplorer/RecallResults.gif "RecallResults")

## <a name="enhanced-results-grid-control"></a>強化された結果グリッドコントロール

テーブルグリッドでは、複数の行、列、およびセルを選択できます。 複数のセル (Excel など) を選択してデータをピボットすることによって集計を計算します。

![アニメーション GIF。Azure データエクスプローラーでピボットモードを有効にした後、列をピボットテーブルのターゲット領域にドラッグすると、集計データが表示されます。](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Intellisense & の書式設定

"Shift + Alt + F" ショートカットキー、コードの折りたたみ (アウトライン)、および IntelliSense を使用して、非常に印刷形式を使用できます。

![Azure データエクスプローラークエリを示すアニメーション GIF。クエリが展開されると、1行に表示され、ピンクの列名を持つ形式が変更されます。](./Images/KustoTools-WebExplorer/Formating.gif "フォーマット")

## <a name="deep-linking"></a>ディープリンク

ディープリンクまたはディープリンクだけをコピーすることも、クエリをコピーすることもできます。 次のテンプレートを使用して、クラスター、データベース、およびクエリを含めるように URL をフォーマットすることもできます。

`https://dataexplorer.azure.com/` [ `clusters/` *Cluster* [ `/databases/` *データベース* [ `?` *オプション*]]]

次のオプションを指定できます。

* `workspace=empty`: 新しい空のワークスペースを作成することを示します (以前のクラスター、タブ、およびクエリの再呼び出しは行われません)。



![アニメーション GIF。Azure データエクスプローラーの [共有] メニューが開きます。[クリップボードにリンクする] 項目に対するクエリリンクが表示されます。](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="how-to-provide-feedback"></a>フィードバックの提供方法

ツールを使用してフィードバックを送信できます。
![Azure データエクスプローラーを示すアニメーション GIF。フィードバックアイコンをクリックすると、[Send us フィードバック] ダイアログボックスが表示されます。](./Images/KustoTools-WebExplorer/Feedback.gif "フィードバック")
