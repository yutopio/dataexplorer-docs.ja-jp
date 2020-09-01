---
title: Azure portal を使用した Azure Data Explorer クラスターでのストリーミング インジェストの構成
description: Azure portal を使用して Azure Data Explorer クラスターを構成し、ストリーミング インジェストによるデータの読み込みを開始する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: 417d2b45e53abeba40f099a33880912a9300bc31
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874853"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-the-azure-portal"></a>Azure portal を使用した Azure Data Explorer クラスターでのストリーミング インジェストの構成

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成する

## <a name="enable-streaming-ingestion-on-your-cluster"></a>クラスターでストリーミング インジェストを有効にする

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster-in-the-azure-portal"></a>Azure portal で新しいクラスターを作成しているときにストリーミング インジェストを有効にする

新しい Azure Data Explorer クラスターを作成するときに、ストリーミング インジェストを有効にすることができます。 

**[構成]** タブで、 **[ストリーミング インジェスト]**  >  **[オン]** を選択します。

:::image type="content" source="media/ingest-data-streaming/cluster-creation-enable-streaming.png" alt-text="Azure Data Explorer でクラスターを作成するときに、ストリーミング インジェストを有効にする":::

### <a name="enable-streaming-ingestion-on-an-existing-cluster-in-the-azure-portal"></a>Azure portal で既存のクラスターでのストリーミング インジェストを有効にする

1. Azure portal で、Azure Data Explorer クラスターに移動します。 
1. **[設定]** で **[構成]** を選択します。 
1. **[構成]** ウィンドウで、 **[オン]** を選択して **[ストリーミング インジェスト]** を有効にします。
1. **[保存]** を選択します。

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-on.png" alt-text="Azure Data Explorer でストリーミング インジェストを有効にする":::

> [!WARNING]
> ストリーミング インジェストを有効にする前に[制限事項](#limitations)を確認してください。

## <a name="create-a-target-table-and-define-the-policy-in-the-azure-portal"></a>Azure portal でターゲット テーブルを作成し、ポリシーを定義する

1. Azure portal で、お使いのクラスターに移動します。
1. **[クエリ]** を選択します。

    :::image type="content" source="media/ingest-data-streaming/cluster-select-query-tab.png" alt-text="Azure Data Explorer ポータルで [クエリ] を選択してストリーミング インジェストを有効にする":::

1. ストリーミング インジェスト経由でデータを受け取るテーブルを作成するには、次コマンドを **[クエリ] ペイン**にコピーし、 **[実行]** を選択します。

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    :::image type="content" source="media/ingest-data-streaming/create-table.png" alt-text="Azure Data Explorer へのストリーミング インジェスト用のテーブルを作成する":::

1. 作成したテーブルまたはそのテーブルを含むデータベースで、[ストリーミング インジェスト ポリシー](kusto/management/streamingingestionpolicy.md)を定義します。 
 
    > [!TIP]
    > データベース レベルで定義されているポリシーは、データベース内の既存および将来のすべてのテーブルに適用されます。 
    
1. 次のコマンドのいずれかを **[クエリ] ペイン** にコピーし、 **[実行]** を選択します。

    ```kusto
    .alter table TestTable policy streamingingestion enable
    ```

    or

    ```kusto
    .alter database StreamingTestDb policy streamingingestion enable
    ```

    :::image type="content" source="media/ingest-data-streaming/define-streaming-ingestion-policy.png" alt-text="Azure Data Explorer でストリーミング インジェスト ポリシーを定義する":::

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

## <a name="drop-the-streaming-ingestion-policy-in-the-azure-portal"></a>Azure portal でストリーミング インジェスト ポリシーをドロップする

1. Azure portal で、Azure Data Explorer クラスターに移動し、 **[クエリ]** を選択します。 
1. ストリーミング インジェストポリシーをテーブルからドロップするには、次のコマンドを **[クエリ] ペイン**にコピーし、 **[実行]** を選択します。

    ```Kusto
    .delete table TestTable policy streamingingestion 
    ```

    :::image type="content" source="media/ingest-data-streaming/delete-streaming-ingestion-policy.png" alt-text="Azure Data Explorer でストリーミング インジェスト ポリシーを削除する":::

1. **[設定]** で **[構成]** を選択します。
1. **[構成]** ウィンドウで、 **[オフ]** を選択して **[ストリーミング インジェスト]** を無効にします。
1. **[保存]** を選択します。

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-off.png" alt-text="Azure Data Explorer でストリーミング インジェストを無効にする":::

[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md)
