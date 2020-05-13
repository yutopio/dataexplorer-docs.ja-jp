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
ms.openlocfilehash: ce7751d7ac60d23f9ffa0fc84992050fe1036131
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370482"
---
# <a name="azure-data-explorer-tools"></a>Azure Data Explorer ツール

## <a name="ad-hoc-query-tools"></a>アドホック クエリ ツール


* [Kusto.Explorer](./kusto-explorer.md) - Kusto のクエリと制御を行うための主要なデスクトップ ツール
* [Web UI](../../web-query-data.md) - Kusto に対するクエリを実行するための Web UI

## <a name="visualizations-dashboards-and-reporting-tools"></a>視覚化、ダッシュボード、およびレポート ツール


* [Azure Notebooks](../../azure-notebooks.md) - Azure Notebooks を使用して Azure Data Explorer のデータを分析します。
* Excel
    * [Excel の空のクエリ](../../excel-blank-query.md) - Excel データ ソースとして Kusto クエリを追加します
    * [Excel コネクタ](../../excel-connector.md) - Azure Data Explorer 用の Excel コネクタ 

* PowerBI

   * [PowerBI ベスト プラクティス](../../power-bi-best-practices.md)
   * [PowerBI コネクタ](../../power-bi-connector.md)
   * [Power BI インポート済みクエリ](../../power-bi-imported-query.md) 
   * [PowerBI SQL クエリ](../../power-bi-sql-query.md)

* [Grafana](../../grafana.md)

## <a name="orchestration-tools"></a>オーケストレーション ツール


* Microsoft Flow
    * [Microsoft Flow コネクタ](../../flow.md)
    * [Microsoft Flow コネクタの使用例](../../flow-usage.md)
* [Microsoft Logic Apps](./logicapps.md) - [Microsoft Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) の一部として Kusto クエリを自動的に実行します



## <a name="data-ingestion-tools"></a>データ取り込みツール


* [LightIngest](../../lightingest.md) - Azure Data Explorer へのアドホック データ取り込みのヘルプ ユーティリティ
 



## <a name="source-control-integration-tools"></a>ソース管理の統合ツール

* [Azure Pipelines](../../devops.md) - パイプラインの一部として制御コマンドを呼び出します
* [Sync Kusto](./synckusto.md) - Git との間で Kusto のストアド関数を同期します
