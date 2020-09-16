---
title: Azure Data Explorer Go SDK を使用してデータを取り込む
description: この記事では、Go SDK を使用して Azure Data Explorer にデータを取り込む (読み込む) 方法について学習します。
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/10/2020
ms.openlocfilehash: 82302fc2071eca8bf2fb1e4c89b96de50b1a8806
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557345"
---
# <a name="ingest-data-using-the-azure-data-explorer-go-sdk"></a>Azure Data Explorer Go SDK 使用してデータを取り込む 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure Data Explorer サービスとやり取りするための [Go SDK クライアント ライブラリ](kusto/api/golang/kusto-golang-client-library.md)が提供されます。 [Go SDK](https://github.com/Azure/azure-kusto-go) を使用して、Azure Data Explorer クラスター内のデータを取り込み、制御し、クエリを実行できます。 

この記事ではまず、テスト クラスター内にテーブルとデータ マッピングを作成します。 その後、Go SDK を使用してクラスターに対するインジェストをキューに登録し、結果を確認します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) のインストール。
* この [Go SDK の最小要件](kusto/api/golang/kusto-golang-client-library.md#minimum-requirements)に従って、[Go](https://golang.org/) をインストールします。 
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成します。
* [アプリの登録を作成し、データベースに対するアクセス許可を付与](provision-azure-ad-app.md)します。 後で使用するために、クライアント ID とクライアント シークレットを保存します。

## <a name="install-the-go-sdk"></a>Go SDK をインストールする

[Go モジュール](https://golang.org/ref/mod)を使用するサンプル アプリケーションを実行すると、Azure Data Explorer Go SDK が自動的にインストールされます。 別のアプリケーション用の Go SDK をインストールした場合は、次の例のように、Go モジュールを作成し、(`go get` を使用して) Azure Data Explorer パッケージをフェッチします。

```console
go mod init foo.com/bar
go get github.com/Azure/azure-kusto-go/kusto
```

パッケージの依存関係は `go.mod` ファイルに追加されます。 これは Go アプリケーションで使用します。

## <a name="review-the-code"></a>コードの確認

この「[コードの確認](#review-the-code)」セクションは省略可能です。 コードのしくみに関心がある場合は、次のコード スニペットで確認できます。 それ以外の場合は、「[アプリケーションの実行](#run-the-application)」に進んでください。

### <a name="authenticate"></a>認証

何らかの操作を実行する前に、プログラムから Azure Data Explorer サービスに対して認証を行う必要があります。

```go
auth := kusto.Authorization{Config: auth.NewClientCredentialsConfig(clientID, clientSecret, tenantID)}
client, err := kusto.New(kustoEndpoint, auth)
```

[kusto.Authorization](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Authorization) のインスタンスは、サービス プリンシパルの資格情報を使用して作成されます。 その後、[kusto.Client](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client) を作成するために使用されます。その際に、クラスター エンドポイントも受け入れる[新しい](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#New])関数が使用されます。

### <a name="create-table"></a>テーブルの作成

create table コマンドは、[Kusto ステートメント](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Stmt)によって表されます。[Mgmt](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client.Mgmt) 関数は、管理コマンドを実行するために使用されます。 これは、コマンドを実行してテーブルを作成するために使用されます。 

```go
func createTable(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createTableCommand))
    if err != nil {
        log.Fatal("failed to create table", err)
    }
    log.Printf("Table %s created in DB %s\n", kustoTable, kustoDB)
}
```

> [!TIP]
> セキュリティを強化するため、既定では、Kusto ステートメントは定数となります。 [`NewStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#NewStmt) で文字列定数が受け入れられます。 [`UnsafeStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#UnsafeStmt) API で非定数のステートメント セグメントを使用できますが、推奨されません。

Kusto の create table コマンドは、次のようになります。

```kusto
.create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
```

### <a name="create-mapping"></a>マッピングを作成する

インジェスト中にデータ マッピングが使用され、受信データが Azure Data Explorer テーブル内の列にマップされます。 詳細については、「[データ マッピング](kusto/management/mappings.md)」を参照してください。 テーブルと同じ方法でマッピングが作成されます。その場合、`Mgmt` 関数を使用し、データベース名と適切なコマンドを指定します。 完全なコマンドは、[サンプルの GitHub リポジトリ](https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data/blob/main/main.go#L20)で入手できます。

```go
func createMapping(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createMappingCommand))
    if err != nil {
        log.Fatal("failed to create mapping - ", err)
    }
    log.Printf("Mapping %s created\n", kustoMappingRefName)
}
```

### <a name="ingest-data"></a>データの取り込み

インジェストは、既存の Azure Blob Storage コンテナーのファイルを使用してキューに登録されます。 

```go
func ingestFile(kc *kusto.Client, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable string) {
    kIngest, err := ingest.New(kc, kustoDB, kustoTable)
    if err != nil {
        log.Fatal("failed to create ingestion client", err)
    }
    blobStorePath := fmt.Sprintf(blobStorePathFormat, blobStoreAccountName, blobStoreContainer, blobStoreFileName, blobStoreToken)
    err = kIngest.FromFile(context.Background(), blobStorePath, ingest.FileFormat(ingest.CSV), ingest.IngestionMappingRef(kustoMappingRefName, ingest.CSV))

    if err != nil {
        log.Fatal("failed to ingest file", err)
    }
    log.Println("Ingested file from -", blobStorePath)
}
```

[インジェスト](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion) クライアントは、[ingest.New](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#New) を使用して作成されます。 [FromFile](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion.FromFile) 関数は、Azure Blob Storage URI を参照するために使用されます。 マッピング参照名とデータ型は [FileOption](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#FileOption) の形式で渡されます。 

## <a name="run-the-application"></a>アプリケーションの実行

1. GitHub から次のサンプル コードをクローンします。

    ```console
    git clone https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data.git
    cd Azure-Data-Explorer-Go-SDK-example-to-ingest-data
    ```

1. `main.go` のこのスニペットに示されているサンプル コードを実行します。 

    ```go
    func main {
        ...
        dropTable(kc, kustoDB)
        createTable(kc, kustoDB)
        createMapping(kc, kustoDB)
        ingestFile(kc, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable)
        ...
    }
    ```

    > [!TIP]
    > 操作のさまざまな組み合わせを試すために、`main.go` 内の各関数をコメント解除したり、コメント化したりすることができます。

    サンプル コードを実行すると、次のアクションが行われます。
    
    1. **テーブルを削除する**: `StormEvents` テーブルが削除されます (存在する場合)。
    1. **テーブルの作成**: `StormEvents` テーブルが作成されます。
    1. **マッピングの作成**: `StormEvents_CSV_Mapping` マッピングが作成されます。
    1. **ファイルのインジェスト**: (Azure Blob Storage 内の) CSV ファイルがインジェスト用にキューに登録されます。

1. 認証用のサービス プリンシパルを作成するには、Azure CLI を使用して [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを指定します。 プログラムによって使用される環境変数の形式で、クラスター エンドポイントとデータベース名を指定してサービス プリンシパル情報を設定します。

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export AZURE_SP_TENANT_ID="<replace with tenant>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. 以下のプログラムを実行します。

    ```console
    go run main.go
    ```

    次のような出力が表示されます。

    ```console
    Connected to Azure Data Explorer
    Using database - testkustodb
    Failed to drop StormEvents table. Maybe it does not exist?
    Table StormEvents created in DB testkustodb
    Mapping StormEvents_CSV_Mapping created
    Ingested file from - https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D
    ```

## <a name="validate-and-troubleshoot"></a>確認してトラブルシューティングを行う

キューに登録されたインジェストでインジェスト プロセスがスケジュールされ、Azure Data Explorer にデータが読み込まれるまで、5 分から 10 分待ちます。 

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインして、クラスターに接続します。 その後、次のコマンドを実行して、`StormEvents` テーブル内のレコードの数を取得します。

    ```kusto
    StormEvents | count
    ```

2. データベースで次のコマンドを実行し、過去 4 時間以内にインジェスト エラーがあったかどうかを調べます。 実行する前にデータベース名を置き換えてください。

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. 次のコマンドを実行し、過去 4 時間以内のすべてのインジェスト操作の状態を表示します。 実行する前にデータベース名を置き換えてください。

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他の記事に進む場合は、作成したリソースをそのままにします。 そのようにしない場合は、データベースで次のコマンドを実行し、`StormEvents` テーブルを削除します。

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>次のステップ

[クエリを作成する](write-queries.md)
