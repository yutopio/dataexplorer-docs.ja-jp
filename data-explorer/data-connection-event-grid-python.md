---
title: Python を使用して Azure Data Explorer 用に Event Grid データ接続を作成する
description: この記事では、Python を使用して Azure Data Explorer 用に Event Grid データ接続を作成する方法について学習します。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: 1c81f68c8892df9a066fd22d3c5ddae2f30c9e1b
ms.sourcegitcommit: 3a2d2def8d6bf395bbbb3b84935bc58adae055b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98635975"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Python を使用して Azure Data Explorer 用に Event Grid データ接続を作成する

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager テンプレート](data-connection-event-grid-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
この記事では、Python を使用して Azure Data Explorer 用に Event Grid データ接続を作成します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Python 3.4 以上](https://www.python.org/downloads/)。
* [クラスターとデータベース](create-cluster-database-python.md)。
* [テーブルと列のマッピング](./net-sdk-ingest-data.md#create-a-table-on-your-test-cluster)。
* [データベースとテーブルのポリシー](database-table-policies-csharp.md) (省略可能)。
* [Event Grid サブスクリプションのあるストレージ アカウント](ingest-data-event-grid.md)。

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Event Grid データ接続を追加する

次の例は、Event Grid データ接続をプログラムを使用して追加する方法を示しています。 Azure portal を使用して Event Grid データ接続を追加する方法については、「[Azure Data Explorer でイベント グリッド データ接続を作成する](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)」を参照してください。


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
blob_storage_event_type = "Microsoft.Storage.BlobCreated"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format,
                                                                                blob_storage_event_type=blob_storage_event_type))
```
|**設定** | **推奨値** | **フィールドの説明**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | テナント ID。 ディレクトリ ID とも呼ばれます。|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | リソースの作成に使用するサブスクリプション ID。|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント ID。|
| client_secret | *xxxxxxxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント シークレット。 |
| resource_group_name | *testrg* | ご利用のクラスターを含むリソース グループの名前。|
| cluster_name | *mykustocluster* | ご利用のクラスターの名前。|
| database_name | *mykustodatabase* | ご利用のクラスター内のターゲット データベースの名前。|
| data_connection_name | *myeventhubconnect* | データ接続の任意の名前。|
| table_name | *StormEvents* | ターゲット データベース内のターゲット テーブルの名前。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | ターゲット テーブルに関連付けられている列マッピングの名前。|
| data_format | *csv* | メッセージのデータ形式。|
| event_hub_resource_id | *リソース ID* | Event Grid がイベントを送信するように構成されているイベント ハブのリソース ID。 |
| storage_account_resource_id | *リソース ID* | インジェスト用のデータを保持しているストレージ アカウントのリソース ID。 |
| consumer_group | *$Default* | ご利用のイベント ハブのコンシューマー グループ。|
| location | *米国中部* | データ接続リソースの場所。|
| blob_storage_event_type | *Microsoft.Storage.BlobCreated* | インジェストをトリガーするイベントの種類。 サポートされているイベントは次のとおりです。Microsoft.Storage.BlobCreated または Microsoft.Storage.BlobRenamed。 BLOB の名前変更は、ADLSv2 ストレージに対してのみサポートされています。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
