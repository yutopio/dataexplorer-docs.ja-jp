---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: orspodek
ms.openlocfilehash: 13601823f9bff9b00d285a215a6cef5160203b37
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375831"
---
Power BI Desktop にデータを取り込んだら、そのデータに基づいてレポートを作成することができます。 州ごとの作物の被害を示す縦棒グラフを使った単純なレポートを作成します。

1. メイン Power BI ウィンドウの左側で、レポート ビューを選択します。

    ![レポート ビュー](media/data-explorer-power-bi-visualize-basic/report-view.png)

1. **[視覚化]** ウィンドウで、集合縦棒グラフを選択します。

    ![縦棒グラフを追加する](media/data-explorer-power-bi-visualize-basic/add-column-chart.png)

    空のグラフがキャンバスに追加されます。

    ![空のグラフ](media/data-explorer-power-bi-visualize-basic/blank-chart.png)

1. **[フィールド]** の一覧で、**DamageCrops** と **State** を選択します。

    ![フィールドを選択する](media/data-explorer-power-bi-visualize-basic/select-fields.png)

    作物への被害を示すグラフが完成しました。このグラフは、テーブル内の上位 1,000 行を対象としています。

    ![州ごとの作物の被害](media/data-explorer-power-bi-visualize-basic/damage-column-chart.png)

1. レポートを保存します。