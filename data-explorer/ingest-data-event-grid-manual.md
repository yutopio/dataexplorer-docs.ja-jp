---
title: ストレージ アカウントに Event Grid サブスクリプションを作成する - Azure Data Explorer
description: この記事では、Azure Data Explorer でストレージ アカウントに Event Grid サブスクリプションを作成する方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 5ea03b982a42232eb1dda728e1df04952fc1183d
ms.sourcegitcommit: 3a2d2def8d6bf395bbbb3b84935bc58adae055b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98635941"
---
# <a name="manually-create-resources-for-event-grid-ingestion"></a>Event Grid インジェスト用のリソースを手動で作成する

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-event-grid.md)
> * [ポータル - リソースを手動で作成する](ingest-data-event-grid-manual.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager テンプレート](data-connection-event-grid-resource-manager.md)

Azure Data Explorer では、[Event Grid インジェスト パイプライン](ingest-data-event-grid-overview.md)を使用して Azure Storage (Azure Blob Storage と Azure Data Lake Storage Gen2) からの継続的インジェストを提供します。 Event Grid インジェスト パイプラインでは、Azure Event Grid サービスにより、BLOB の作成または BLOB の名前変更のイベントは Azure Event Hub 経由でストレージ アカウントから Azure Data Explorer にルーティングされます。

この記事では、Event Grid インジェストに必要なリソースを手動で作成する方法について説明します。Event Grid サブスクリプション、イベント ハブ名前空間、およびイベント ハブ。 イベント ハブ名前空間とイベント ハブの作成については、「[前提条件](#prerequisites)」をご覧ください。 Event Grid インジェストの定義中にこれらのリソースの自動作成を使用するには、「[Azure Data Explorer でイベント グリッド データ接続を作成する](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [無料の Azure アカウント](https://azure.microsoft.com/free/)を作成します。
* [クラスターとデータベース](create-cluster-database-portal.md)。
* [ストレージ アカウント](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)。
    * Event Grid 通知サブスクリプションは、`BlobStorage`、`StorageV2`、または [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) に対して Azure Storage アカウントで設定できます。
* [イベント ハブ名前空間とイベント ハブ](/azure/event-hubs/event-hubs-create)

> [!NOTE]
> 最適なパフォーマンスを得るには、Azure Data Explorer クラスターと同じリージョンにすべてのリソースを作成します。

## <a name="create-an-event-grid-subscription"></a>Event Grid のサブスクリプションを作成する
 
1. Azure portal で、ストレージ アカウントに移動します。
1. 左側のメニューで、 **[イベント]**  >  **[イベント サブスクリプション]** を選択します。

     :::image type="content" source="media/eventgrid/create-event-grid-subscription-1.png" alt-text="Event Grid サブスクリプションを作成する":::

1. **[基本]** タブの **[イベント サブスクリプションの作成]** ウィンドウで、次の値を指定します。

    :::image type="content" source="media/eventgrid/create-event-grid-subscription-2.png" alt-text="入力するイベント サブスクリプションの値を作成する":::

    |**設定** | **推奨値** | **フィールドの説明**|
    |---|---|---|
    | 名前 | *test-grid-connection* | 作成するイベント グリッド サブスクリプションの名前。|
    | イベント スキーマ | *イベント グリッド スキーマ* | イベント グリッドで使用するスキーマ。 |
    | トピックの種類 | *ストレージ アカウント* | イベント グリッド トピックの種類。 自動的に設定されます。|
    | ソース リソース | *gridteststorage1* | ご利用のストレージ アカウントの名前。 自動的に設定されます。|
    | [システム トピック名] | *gridteststorage1...* | Azure Storage によってイベントが発行されるシステム トピック。 次に、このシステム トピックにより、イベントを受信して処理するサブスクライバーにイベントが転送されます。 自動的に設定されます。|
    | イベントの種類のフィルター | *作成された BLOB* | 通知を取得する特定のイベント。 サブスクリプションを作成する場合、サポートされている種類のいずれかを選択します。Microsoft.Storage.BlobCreated または Microsoft.Storage.BlobRenamed。 BLOB の名前変更は、ADLSv2 ストレージに対してのみサポートされています。 |

1. **[エンドポイントの詳細]** で、 **[イベント ハブ]** を選択します。

    :::image type="content" source="media/eventgrid/endpoint-details.png" alt-text="イベントを受け取るイベント ハンドラーを選択する - イベント ハブ - Azure Data Explorer":::

1. **[エンドポイントの選択]** をクリックし、作成したイベント ハブを入力します (*test-hub* など)。
    
1. 特定のサブジェクトを追跡する場合は、 **[フィルター]** タブを選択します。 次のように、通知用のフィルターを設定します。
   
    :::image type="content" source="media/eventgrid/filters-tab.png" alt-text="Event Grid の [フィルター] タブ":::

   1. **[サブジェクト フィルタリングを有効にする]** を選択します。
   1. **[次で始まるサブジェクト]** フィールドは、サブジェクトの "*リテラル*" プレフィックスです。 適用されるパターンは *startswith* であるため、複数のコンテナー、フォルダー、BLOB を対象にできます。 ワイルドカードは使用できません。
       * BLOB コンテナーに対してフィルターを定義するには、フィールドを *`/blobServices/default/containers/[container prefix]`* のように設定します。
       * BLOB プレフィックス (または Azure Data Lake Gen2 のフォルダー) に対してフィルターを定義するには、フィールドを次のように設定します。 *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`*
   1. **[Subject Ends With]\(指定の値で終わる件名\)** フィールドは、BLOB の "*リテラル*" サフィックスです。 ワイルドカードは使用できません。
   1. **[Case-sensitive subject matching]\(大文字小文字を区別した件名の一致\)** フィールドは、プレフィックスとサフィックスのフィルターで大文字と小文字が区別されるかどうかを示します。

    イベントのフィルター処理の詳細については、[Blob Storage のイベント](/azure/storage/blobs/storage-blob-event-overview#filtering-events)に関するページをご覧ください。

1. **[作成]**

## <a name="next-steps"></a>次のステップ

* 設定を続行し、Azure portal の 「[Azure Data Explorer で Event Grid データ接続を作成する](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)」を使用して Azure Data Explorer へのデータ インジェスト接続を作成します。

* 作成したリソースを使用して Event Grid インジェストを継続する予定がなく、もうそのリソースを使用しない場合は、コストが発生しないように[リソースをクリーンアップ](ingest-data-event-grid.md#clean-up-resources)してください。
