---
title: Azure Go SDK を使用して Azure Data Explorer クラスターとデータベースを作成する
description: Azure Go SDK を使用して Azure Data Explorer クラスターとデータベースを作成、一覧表示、削除する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 3a8133c42ad87ec7eec693be3109ce5e7aea4935
ms.sourcegitcommit: 79d923d7b7e8370726974e67a984183905f323ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2020
ms.locfileid: "96868639"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-go"></a>Go を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [ARM テンプレート](create-cluster-database-resource-manager.md)

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージドのデータ分析サービスです。 Azure Data Explorer を使用するには、最初にクラスターを作成し、そのクラスター内に 1 つまたは複数のデータベースを作成します。 その後、クエリを実行できるように、データをデータベースに取り込み (読み込み) ます。 

この記事では、[Go](https://golang.org/) を使用して Azure Data Explorer クラスターとデータベースを作成します。 その後、新しいクラスターとデータベースを一覧表示および削除したり、リソースに対して操作を実行したりできます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free)を作成してください。
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) のインストール。
* Go の適切なバージョンをインストールします。 サポートされているリリースの詳細については、[Azure Go SDK](https://github.com/Azure/azure-sdk-for-go) に関するページを参照してください。

## <a name="review-the-code"></a>コードの確認

このセクションは省略可能です。 コードのしくみに関心がある場合は、次のコード スニペットで確認できます。 それ以外の場合は、「[アプリケーションの実行](#run-the-application)」に進んでください。

### <a name="authentication"></a>認証

何らかの操作を実行する前に、プログラムによって Azure Data Explorer に対する認証が行われる必要があります。 [クライアント資格情報の認証の種類](/azure/developer/go/azure-sdk-authorization#use-environment-based-authentication)が、[auth.NewAuthorizerFromEnvironment](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromEnvironment) によって使用されます。これによって、次の定義済み環境変数が検索されます: `AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET`、`AZURE_TENANT_ID`。

次の例では、この手法を使用して [kusto.ClustersClient](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v48.2.0+incompatible/services/kusto/mgmt/2020-02-15/kusto) が作成される方法を示します。

```go
func getClustersClient(subscription string) kusto.ClustersClient {
    client := kusto.NewClustersClient(subscription)
    authR, err := auth.NewAuthorizerFromEnvironment()
    if err != nil {
        log.Fatal(err)
    }
    client.Authorizer = authR

    return client
}
```

> [!TIP]
> Azure CLI がインストールされて認証用に構成されている場合は、ローカル開発に [auth.NewAuthorizerFromCLIWithResource](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) 関数を使用します。

### <a name="create-cluster"></a>クラスターの作成

`kusto.ClustersClient` で [CreateOrUpdate](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto) 関数を使用して、新しい Azure Data Explorer クラスターを作成します。 プロセスが完了するのを待ってから、結果を調べます。

```go
func createCluster(sub, name, location, rgName string) {
    ...
    result, err := client.CreateOrUpdate(ctx, rgName, name, kusto.Cluster{Location: &location, Sku: &kusto.AzureSku{Name: kusto.DevNoSLAStandardD11V2, Capacity: &numInstances, Tier: kusto.Basic}})
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
}
```

### <a name="list-clusters"></a>クラスターを一覧表示する

`kusto.ClustersClient` で [ListByResourceGroup](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClustersClient.ListByResourceGroup) 関数を使用して、[kusto.ClusterListResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClusterListResult) を取得します。次に、それが反復されて、表形式で出力が表示されます。


```go
func listClusters(sub, rgName string) {
    ...
    result, err := getClustersClient(sub).ListByResourceGroup(ctx, rgName)
    ...
    for _, c := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="create-database"></a>データベースの作成

[kusto.DatabasesClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient) で [CreateOrUpdate](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.CreateOrUpdate) 関数を使用して、既存のクラスターに新しい Azure Data Explorer データベースを作成します。 プロセスが完了するのを待ってから、結果を調べます。


```go
func createDatabase(sub, rgName, clusterName, location, dbName string) {
    future, err := client.CreateOrUpdate(ctx, rgName, clusterName, dbName, kusto.ReadWriteDatabase{Kind: kusto.KindReadWrite, Location: &location})
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    ...
}
```

### <a name="list-databases"></a>データベースを一覧表示する

`kusto.DatabasesClient` で [ListByCluster](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.ListByCluster) 関数を使用して、[kusto.DatabaseListResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabaseListResult) を取得します。次に、それが反復されて、表形式で出力が表示されます。


```go
func listDatabases(sub, rgName, clusterName string) {
    result, err := getDBClient(sub).ListByCluster(ctx, rgName, clusterName)
    ...
    for _, db := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="delete-database"></a>データベースの削除

`kusto.DatabasesClient` で [Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.Delete) 関数を使用して、クラスター内の既存のデータベースを削除します。 プロセスが完了するのを待ってから、結果を調べます。

```go
func deleteDatabase(sub, rgName, clusterName, dbName string) {
    ...
    future, err := getDBClient(sub).Delete(ctx, rgName, clusterName, dbName)
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

### <a name="delete-cluster"></a>クラスターを削除する

`kusto.ClustersClient` で [Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClustersClient.Delete) 関数を使用して、クラスターを削除します。 プロセスが完了するのを待ってから、結果を調べます。

```go
func deleteCluster(sub, clusterName, rgName string) {
    result, err := client.Delete(ctx, rgName, clusterName)
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

## <a name="run-the-application"></a>アプリケーションの実行

サンプル コードをそのまま実行すると、次のアクションが行われます。
    
1. Azure Data Explorer クラスターが作成されます。
1. 指定されたリソース グループ内のすべての Azure Data Explorer クラスターが一覧表示されます。
1. Azure Data Explorer データベースが既存のクラスター内に作成されます。
1. 指定されたクラスター内のすべてのデータベースが一覧表示されます。
1. データベースが削除されます。
1. クラスターが削除されます。


    ```go
    func main() {
        createCluster(subscription, clusterNamePrefix+clusterName, location, rgName)
        listClusters(subscription, rgName)
        createDatabase(subscription, rgName, clusterNamePrefix+clusterName, location, dbNamePrefix+databaseName)
        listDatabases(subscription, rgName, clusterNamePrefix+clusterName)
        deleteDatabase(subscription, rgName, clusterNamePrefix+clusterName, dbNamePrefix+databaseName)
        deleteCluster(subscription, clusterNamePrefix+clusterName, rgName)
    }
    ```

    > [!TIP]
    > 操作のさまざまな組み合わせを試すために、必要に応じて、`main.go` 内の各関数をコメント解除したり、コメント化したりすることができます。

1. GitHub から次のサンプル コードをクローンします。

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-go-cluster-management.git
    cd azure-data-explorer-go-cluster-management
    ```

1. プログラムにより、クライアントの資格情報を使用して認証が行われます。 Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用してサービス プリンシパルを作成します。 次の手順で使用するために、クライアント ID、クライアント シークレット、およびテナント ID の情報を保存します。

1. サービス プリンシパル情報を含む必要な環境変数をエクスポートします。 クラスターを作成するサブスクリプション ID、リソース グループ、リージョンを入力します。

    ```console
    export AZURE_CLIENT_ID="<enter service principal client ID>"
    export AZURE_CLIENT_SECRET="<enter service principal client secret>"
    export AZURE_TENANT_ID="<enter tenant ID>"

    export SUBSCRIPTION="<enter subscription ID>"
    export RESOURCE_GROUP="<enter resource group name>"
    export LOCATION="<enter azure location e.g. Southeast Asia>"

    export CLUSTER_NAME_PREFIX="<enter prefix (cluster name will be [prefix]-ADXTestCluster)>"
    export DATABASE_NAME_PREFIX="<enter prefix (database name will be [prefix]-ADXTestDB)>"
    ```
    
    > [!TIP]
    > 運用シナリオでは、環境変数を使用してアプリケーションに資格情報を提供する可能性が高くなります。 「[コードの確認](#review-the-code)」で説明されているように、ローカル開発では、Azure CLI がインストールされて認証用に構成されている場合、[auth.NewAuthorizerFromCLIWithResource](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) を使用します。 その場合、サービス プリンシパルを作成する必要はありません。


1. 以下のプログラムを実行します。

    ```console
    go run main.go
    ```

    次のような出力が表示されます。

    ```console
    waiting for cluster creation to complete - fooADXTestCluster
    created cluster fooADXTestCluster
    listing clusters in resource group <your resource group>
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    |       NAME        |  STATE  |    LOCATION    | INSTANCES |                            URI                           |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    | fooADXTestCluster | Running | Southeast Asia |         1 | https://fooADXTestCluster.southeastasia.kusto.windows.net |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    
    waiting for database creation to complete - barADXTestDB
    created DB fooADXTestCluster/barADXTestDB with ID /subscriptions/<your subscription ID>/resourceGroups/<your resource group>/providers/Microsoft.Kusto/Clusters/fooADXTestCluster/Databases/barADXTestDB and type Microsoft.Kusto/Clusters/Databases
    
    listing databases in cluster fooADXTestCluster
    +--------------------------------+-----------+----------------+------------------------------------+
    |              NAME              |   STATE   |    LOCATION    |                TYPE                |
    +--------------------------------+-----------+----------------+------------------------------------+
    | fooADXTestCluster/barADXTestDB | Succeeded | Southeast Asia | Microsoft.Kusto/Clusters/Databases |
    +--------------------------------+-----------+----------------+------------------------------------+
    
    waiting for database deletion to complete - barADXTestDB
    deleted DB barADXTestDB from cluster fooADXTestCluster

    waiting for cluster deletion to complete - fooADXTestCluster
    deleted ADX cluster fooADXTestCluster from resource group <your resource group>
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事のサンプル コードを使用してプログラムでクラスターを削除しなかった場合は、[Azure CLI](create-cluster-database-cli.md#clean-up-resources) を使用して手動で削除します。

## <a name="next-steps"></a>次のステップ

[Azure Data Explorer Go SDK 使用してデータを取り込む](go-ingest-data.md)
