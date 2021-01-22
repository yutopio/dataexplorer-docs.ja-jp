---
title: Azure のデータ エクスプローラーにサンプル データを取り込む
description: Azure のデータ エクスプローラーに気象関連のサンプル データ (負荷) を取り込む方法を説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.localizationpriority: high
ms.openlocfilehash: 5942cd57d2fbc607a5ab80571b0e2937fa29306b
ms.sourcegitcommit: 8c0674d2bc3c2e10eace5314c30adc7c9e4b3d44
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571722"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>クイック スタート:Azure のデータ エクスプローラーにサンプル データを取り込む

この記事では、Azure のデータ エクスプローラー データベースにサンプル データ (負荷) を取り込む方法を示します。 [データを取り込む方法はいくつかあります](ingest-data-overview.md)｡ 今回は、テスト目的に適した基本的なアプローチに注目します。

> [!NOTE]
> 「[Azure Data Explorer の Python ライブラリを使用してデータを取り込む](python-ingest-data.md)」を終えている場合、サンプル データは既にあります｡

## <a name="prerequisites"></a>前提条件

[テスト用のクラスターとデータベース](create-cluster-database-portal.md)

## <a name="ingest-data"></a>データの取り込み

**StormEvents** サンプル データ セットには､ [National Centers for Environmental Information から入手した気象関連データが含まれています](https://www.ncdc.noaa.gov/stormevents/)｡

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインします。

1. アプリケーションの左上にある **[Add cluster]\(クラスターの追加\)** を選択します。

1. **[Add cluster]** ダイアログ ボックスで `https://<ClusterName>.<Region>.kusto.windows.net/` の形式でラスターの URL を入力して､ **[追加]** を選択します。

1. 次のコマンドを貼り付け、 **[実行]** を選択して、StormEvents テーブルを作成します。

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. 次のコマンドを貼り付け、 **[実行]** を選択して、StormEvents テーブルにデータを取り込みます。

    ```Kusto
    .ingest into table StormEvents 'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?sv=2019-12-12&ss=b&srt=o&sp=r&se=2022-09-05T02:23:52Z&st=2020-09-04T18:23:52Z&spr=https&sig=VrOfQMT1gUrHltJ8uhjYcCequEcfhjyyMX%2FSc3xsCy4%3D' with (ignoreFirstRecord=true)
    ```

1. 取り込みが完了したら、次のクエリに貼り付けて､ウィンドウでクエリを選択し､ **[実行]** を選択します。

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    取り込まれたサンプル データから次のクエリ結果が返されます。

    ![Query results](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer でのデータ インジェスト](ingest-data-overview.md)には、インジェスト メソッドに関する詳細があります。
* [クイック スタート: Azure Data Explorer](web-query-data.md) の Web UI でデータにクエリを実行します。
* Kusto クエリ言語で[クエリを記述します](write-queries.md)。
