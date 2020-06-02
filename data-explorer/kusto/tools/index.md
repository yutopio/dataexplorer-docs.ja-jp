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
ms.openlocfilehash: b6bc95158c1dd161a17572342c6a99bdf9d37235
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257911"
---
# <a name="azure-data-explorer-tools"></a>Azure Data Explorer ツール

## <a name="ad-hoc-query-tools"></a>アドホック クエリ ツール

* Kusto.Explorer
   * [Kusto.Explorer のインストールとユーザー インターフェイス](./kusto-explorer.md) - Kusto のクエリと制御を行うための主要なデスクトップ ツール
   * [Kusto.Explorer の使用](./kusto-explorer-using.md)
   * [Kusto.Explorer のトラブルシューティング](kusto-explorer-troubleshooting.md)
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
* [K2Bridge オープンソース コネクタ](../../k2bridge.md) - Kibana で Azure Data Explorer のデータを視覚化する

## <a name="orchestration-tools"></a>オーケストレーション ツール


* Microsoft Flow
    * [Microsoft Flow コネクタ](../../flow.md)
    * [Microsoft Flow コネクタの使用例](../../flow-usage.md)
* [Microsoft Logic Apps](./logicapps.md) - [Microsoft Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) の一部として Kusto クエリを自動的に実行します



## <a name="data-ingestion-tools"></a>データ取り込みツール


* [LightIngest](../../lightingest.md) - Azure Data Explorer へのアドホック データ取り込みのヘルプ ユーティリティ
* [ワンクリック インジェスト](../../ingest-data-one-click.md) - データをすばやく取り込み、テーブルとマッピングの構造を自動的に提案するツール。
* [Azure Data Factory](azure-data-factory.md)


## <a name="source-control-integration-tools"></a>ソース管理の統合ツール

* [Azure Pipelines](../../devops.md) - パイプラインの一部として制御コマンドを呼び出します
* [Sync Kusto](./synckusto.md) - Git との間で Kusto のストアド関数を同期します
