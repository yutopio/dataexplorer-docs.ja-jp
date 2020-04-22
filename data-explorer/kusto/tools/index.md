---
title: Azure Data Explorer ツール - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer の Azure Data Explorer ツールについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: e9b274ae129f7cd15ba30edb24f8432c738f55ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490652"
---
# <a name="azure-data-explorer-tools"></a>Azure Data Explorer ツール

## <a name="ad-hoc-query-tools"></a>アドホック クエリ ツール


* [Kusto.Explorer](./kusto-explorer.md) - Kusto のクエリと制御を行うための主要なデスクトップ ツール
* [Web UI](https://docs.microsoft.com/azure/data-explorer/web-query-data) - Kusto に対するクエリを実行するための Web UI

## <a name="visualizations-dashboards-and-reporting-tools"></a>視覚化、ダッシュボード、およびレポート ツール


* [Azure Notebooks](https://docs.microsoft.com/azure/data-explorer/azure-notebooks) - Azure Notebooks を使用して Azure Data Explorer のデータを分析します。
* Excel
    * [Excel の空のクエリ](https://docs.microsoft.com/azure/data-explorer/excel-blank-query) - Excel データ ソースとして Kusto クエリを追加します
    * [Excel コネクタ](https://docs.microsoft.com/azure/data-explorer/excel-connector) - Azure Data Explorer 用の Excel コネクタ 

* PowerBI

   * [PowerBI ベスト プラクティス](https://docs.microsoft.com/azure/data-explorer/power-bi-best-practices)
   * [PowerBI コネクタ](https://docs.microsoft.com/azure/data-explorer/power-bi-connector)
   * [Power BI インポート済みクエリ](https://docs.microsoft.com/azure/data-explorer/power-bi-imported-query) 
   * [PowerBI SQL クエリ](https://docs.microsoft.com/azure/data-explorer/power-bi-sql-query)

* [Grafana](https://docs.microsoft.com/azure/data-explorer/grafana)

## <a name="orchestration-tools"></a>オーケストレーション ツール


* Microsoft Flow
    * [Microsoft Flow コネクタ](https://docs.microsoft.com/azure/data-explorer/flow)
    * [Microsoft Flow コネクタの使用例](https://docs.microsoft.com/azure/data-explorer/flow-usage)
* [Microsoft Logic Apps](./logicapps.md) - [Microsoft Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) の一部として Kusto クエリを自動的に実行します



## <a name="data-ingestion-tools"></a>データ取り込みツール


* [LightIngest](https://docs.microsoft.com/azure/data-explorer/lightingest) - Azure Data Explorer へのアドホック データ取り込みのヘルプ ユーティリティ
 



## <a name="source-control-integration-tools"></a>ソース管理の統合ツール

* [Azure Pipelines](https://docs.microsoft.com/azure/data-explorer/devops) - パイプラインの一部として制御コマンドを呼び出します
* [Sync Kusto](./synckusto.md) - Git との間で Kusto のストアド関数を同期します
