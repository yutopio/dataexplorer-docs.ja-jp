---
title: Python を使用して Azure Data Explorer クラスターと DB を作成する
description: Python を使用して Azure Data Explorer クラスターとデータベースを作成する方法を学習します。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 494df2c610329da0f31621e293fe652b2fb3a911
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554623"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Python を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [ARM テンプレート](create-cluster-database-resource-manager.md)

この記事では、Python を使用して Azure Data Explorer クラスターとデータベースを作成します。 Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースに取り込み (読み込み) ます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Python 3.4 以上](https://www.python.org/downloads/)。

* [リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパル](/azure/active-directory/develop/howto-create-service-principal-portal)。 `Directory (tenant) ID`、`Application ID`、`Client Secret` の値を取得します。

## <a name="install-python-package"></a>Python パッケージのインストール

Azure Data Explorer (Kusto) 向けの Python パッケージをインストールするには、Python をパスに設定した状態でコマンド プロンプトを開きます。 次のコマンドを実行します。

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>認証
この記事の例を実行するには、リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルが必要です。 「[Azure AD アプリケーションを作成する](/azure/active-directory/develop/howto-create-service-principal-portal)」を参照して、無料の Azure AD アプリケーションを作成し、サブスクリプション スコープでロールの割り当てを追加します。 `Directory (tenant) ID`、`Application ID`、および `Client Secret` を取得する方法も示されています。

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

1. 次のコマンドを使用して、クラスターを作成します。

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kusto_management_client = KustoManagementClient(credentials, subscription_id)

    cluster_operations = kusto_management_client.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    poller.wait()
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | cluster_name | *mykustocluster* | クラスターの任意の名前。|
   | sku_name | *Standard_D13_v2* | クラスターに使用される SKU。 |
   | レベル | *Standard* | SKU レベル。 |
   | capacity | *number* | クラスターのインスタンスの数。 |
   | resource_group_name | *testrg* | クラスターが作成されるリソース グループの名前。 |

    > [!NOTE]
    > **クラスターの作成** は、実行時間の長い操作となります。 メソッド **create_or_update** は LROPoller インスタンスを返します。詳細については、[LROPoller クラス](/python/api/msrest/msrest.polling.lropoller)を参照してください。

1. クラスターが正常に作成されたかどうかを確認するには、次のコマンドを実行します。

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= cluster_name, custom_headers=None, raw=False)
    ```

結果に値が `provisioningState` の `Succeeded` が含まれている場合、クラスターは正常に作成されています。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

1. 次のコマンドを使用して、データベースを作成します。

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.kusto.models import ReadWriteDatabase
    from datetime import timedelta

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
    
    location = 'Central US'
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    soft_delete_period = timedelta(days=3650)
    hot_cache_period = timedelta(days=3650)
    database_name = "mykustodatabase"

    kusto_management_client = KustoManagementClient(credentials, subscription_id)
    
    database_operations = kusto_management_client.databases
    database = ReadWriteDatabase(location=location,
                        soft_delete_period=soft_delete_period,
                        hot_cache_period=hot_cache_period)
    
    poller = database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = cluster_name, database_name = database_name, parameters = database)
    poller.wait()
    ```

    > [!NOTE]
    > Python バージョン 0.4.0 以前を使用している場合は、ReadWriteDatabase ではなく Database を使用します。

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | cluster_name | *mykustocluster* | データベースの作成先となるクラスターの名前。|
   | database_name | *mykustodatabase* | データベースの名前。|
   | resource_group_name | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | soft_delete_period | *3650 days, 0:00:00* | データをクエリに使用できるようにしておく時間。 |
   | hot_cache_period | *3650 days, 0:00:00* | データをキャッシュに保持する時間。 |

1. 次のコマンドを実行して、作成したデータベースを確認します。

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = cluster_name, database_name = database_name)
    ```

クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

* 他の記事に進む場合は、作成したリソースをそのままにします。
* リソースをクリーンアップするには、クラスターを削除します。 クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 クラスターを削除するには次のコマンドを使います。

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = cluster_name)
    ```

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer の Python ライブラリを使用してデータを取り込む](python-ingest-data.md)
