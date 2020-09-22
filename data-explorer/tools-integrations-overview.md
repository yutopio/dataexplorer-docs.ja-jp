---
title: Azure Data Explorer のツールと統合の概要 - Azure Data Explorer
description: この記事では、Azure Data Explorer におけるツールと統合について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: e341b70dfc2a7c0d3038d6d60d9c8ae2b40b6218
ms.sourcegitcommit: c2ab3176db4dd55ac9ca8eee52bbd24096d1277f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2020
ms.locfileid: "90740254"
---
# <a name="azure-data-explorer-tools-and-integrations-overview"></a>Azure Data Explorer のツールと統合の概要

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージド データ分析サービスです。 Azure Data Explorer により、多種多様なデータが収集、格納、分析され、製品の改良、カスタマー エクスペリエンスの強化、デバイスの監視、操作の向上を実現できます。 

Azure Data Explorer には、データ インジェスト、クエリ、視覚化、オーケストレーションなどを行うためのさまざまなツールと統合が用意されています。 Azure Data Explorer では、ネイティブ サービスに加えて、さまざまな製品やプラットフォームと簡単に統合し、さまざまな顧客のユース ケースを実現し、ワークフローを効率化してコストを削減することで、ビジネス プロセスを最適化することができます。 

この記事には、Azure Data Explorer のツール、コネクタ、統合の一覧と、詳細を確認するための関連するドキュメントへのリンクが記載されています。

## <a name="ingest-data"></a>データの取り込み 

データ インジェストとは、1 つまたは複数のソースから Azure Data Explorer にデータ レコードを読み込むために使用するプロセスのことです。 取り込まれたデータは、クエリに使用できるようになります。 Azure Data Explorer には、データ インジェスト用のツールとコネクタがいくつか用意されています。 

### <a name="azure-data-explorer-ingestion-tools"></a>Azure Data Explorer のインジェスト ツール

* [LightIngest](lightingest.md) - Azure Data Explorer へのアドホック データ取り込みのヘルプ ユーティリティ
* ワンクリックでのインジェスト
    * [ワンクリックでのインジェストの概要](ingest-data-one-click.md) 
    * [コンテナーから新しいテーブルにデータを取り込む](one-click-ingestion-new-table.md)
    * [ローカル ファイルから既存のテーブルにデータを取り込む](one-click-ingestion-existing-table.md)

### <a name="ingestion-integrations"></a>インジェストの統合

* イベント ハブ
    * [イベント ハブからの取り込み](ingest-data-event-hub-overview.md)
    * [Azure portal](ingest-data-event-hub.md)、[C#](data-connection-event-hub-csharp.md)、[Python](data-connection-event-hub-python.md) または [Azure Resource Manager テンプレート](data-connection-event-hub-resource-manager.md)を使用したイベント ハブからの取り込み
* Event Grid
    * [Event Grid からの取り込み](ingest-data-event-grid-overview.md)
    * [Azure portal](ingest-data-event-grid.md)、[C#](data-connection-event-grid-csharp.md)、[Python](data-connection-event-grid-python.md) または [Azure Resource Manager テンプレート](data-connection-event-grid-resource-manager.md)を使用した Event Grid からの取り込み
* IoT Hub
    * [IoT Hub からの取り込み](ingest-data-iot-hub-overview.md)
    * [Azure portal](ingest-data-iot-hub.md)、[C#](data-connection-iot-hub-csharp.md)、[Python](data-connection-iot-hub-python.md) または [Azure Resource Manager テンプレート](data-connection-iot-hub-resource-manager.md)を使用した IoT Hub からの取り込み
* [Logstash](ingest-data-logstash.md)
* Azure Data Factory
    * [Azure Data Factory と統合する](data-factory-integration.md)
    * [データをコピーする](data-factory-load-data.md)
    * [Azure Data Factory テンプレートを使用してデータベースから一括コピーする](data-factory-template.md)
    * [Azure Data Factory コマンド アクティビティを使用して Azure Data Explorer 制御コマンドを実行する](data-factory-command-activity.md)
* Apache 
    * [Spark](spark-connector.md)
    * [Kafka](ingest-data-kafka.md)
* [Cosmos DB](https://github.com/Azure/azure-kusto-labs/tree/master/cosmosdb-adx-integration)
* [Power Automate](flow.md)

## <a name="query-data"></a>クエリ データ

### <a name="azure-data-explorer-query-tools"></a>Azure Data Explorer クエリ ツール

Azure Data Explorer クエリを実行するために使用できるツールがいくつかあります。

* Kusto.Explorer
    * [インストールとユーザー インターフェイス](kusto/tools/kusto-explorer.md)
    * [Kusto.Explorer の使用](kusto/tools/kusto-explorer-using.md)
    * [options](kusto/tools/kusto-explorer-options.md)
    * その他のトピックとして、[トラブルシューティング](kusto/tools/kusto-explorer-troubleshooting.md)、[キーボード ショートカット](kusto/tools/kusto-explorer-shortcuts.md)、[コード リファクタリング](kusto/tools/kusto-explorer-refactor.md)、[コード ナビゲーション](kusto/tools/kusto-explorer-codenav.md)、[コード分析](kusto/tools/kusto-explorer-code-analyzer.md)などがあります
* [Web UI](web-query-data.md)
* [Kusto.Cli](kusto/tools/kusto-cli.md)

### <a name="query-integrations"></a>クエリの統合

* [Azure Monitor](query-monitor-data.md)
* [Azure Data Lake](data-lake-query-data.md)
* [Apache Spark](spark-connector.md)
* Microsoft Power Apps
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic)

## <a name="visualizations-dashboards-and-reporting"></a>視覚化、ダッシュボード、レポート

[視覚化の概要](viz-overview.md)に関するページでは、データの視覚化、ダッシュボード、レポートのオプションについて説明します。 

## <a name="notebook-connectivity"></a>Notebook の接続

* [Azure Notebooks](azure-notebooks.md)
* [Jupyter Notebook](kqlmagic.md)
* [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/notebooks-kqlmagic)

## <a name="orchestration"></a>オーケストレーション

* Power Automate
    * [Power Automate コネクタ](flow.md)
    * [Power Automate コネクタの使用例](flow-usage.md)
* [Microsoft ロジック アプリ](kusto/tools/logicapps.md) 
* [Azure Data Factory](data-factory-integration.md)

## <a name="share-data"></a>データの共有

* [Azure Data Share](data-share.md)

## <a name="source-control-integration"></a>ソース管理の統合

* [Azure Pipelines](devops.md) 
* [Kusto の同期](kusto/tools/synckusto.md) 

<!--Open Source Tools-->
