---
title: Azure Data Explorer Java SDK を使用してデータを取り込む
description: この記事では、Java SDK を使用して Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 45b0ce7b1716a4ed2ab7277b6c99a5d95f8ad5af
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906206"
---
# <a name="ingest-data-using-the-azure-data-explorer-java-sdk"></a>Azure Data Explorer Java SDK 使用してデータを取り込む 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Node](node-ingest-data.md)
> * [Go](go-ingest-data.md)
> * [Java](java-ingest-data.md)

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 [Java クライアント ライブラリ](kusto/api/java/kusto-java-client-library.md)を使用すると、Azure Data Explorer クラスターでデータの取り込み、管理コマンドの発行、データに対するクエリを実行できます。

この記事では、Azure Data Explorer の Java ライブラリを使用してデータを取り込む方法について説明します。 まず、テスト クラスター内にテーブルとデータ マッピングを作成します。 その後、Java SDK を使用して Blob Storage からクラスターへのインジェストをキューに登録し、結果を確認します。

## <a name="prerequisites"></a>前提条件

* [無料の Azure アカウント](https://azure.microsoft.com/free/)。
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* JDK バージョン 1.8 以降。
* [Maven](https://maven.apache.org/download.cgi)。
* [クラスターとデータベース](create-cluster-database-portal.md)。
* [アプリの登録を作成し、データベースに対するアクセス許可を付与](provision-azure-ad-app.md)します。 後で使用するために、クライアント ID とクライアント シークレットを保存します。

## <a name="review-the-code"></a>コードの確認

このセクションは省略可能です。 コードの動作方法については、次のコード スニペットを確認してください。 このセクションをスキップするには、「[アプリケーションの実行](#run-the-application)」に進んでください。

### <a name="authentication"></a>認証

このプログラムでは、ConnectionStringBuilder で Azure Active Directory 認証資格情報が使用されます。

1. クエリと管理のために `com.microsoft.azure.kusto.data.Client` を作成します。

    ```java
    static Client getClient() throws Exception {
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(endpoint, clientID, clientSecret, tenantID);
        return ClientFactory.createClient(csb);
    }
    ```

1. `com.microsoft.azure.kusto.ingest.IngestClient` を作成して使用し、Azure Data Explorer へのデータ インジェストをキューに入れます。

    ```java
    static IngestClient getIngestionClient() throws Exception {
        String ingestionEndpoint = "https://ingest-" + URI.create(endpoint).getHost();
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(ingestionEndpoint, clientID, clientSecret);
        return IngestClientFactory.createClient(csb);
    }
    ```

### <a name="management-commands"></a>管理コマンド

[`.drop`](kusto/management/drop-function.md) や [`.create`](kusto/management/create-function.md) などの[管理コマンド](kusto/management/commands.md)は、`com.microsoft.azure.kusto.data.Client` オブジェクトで `execute` を呼び出して実行されます。

たとえば、`StormEvents` テーブルは次のように作成されます。

```java
static final String createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)";

static void createTable(String database) {
    try {
        getClient().execute(database, createTableCommand);
        System.out.println("Table created");
    } catch (Exception e) {
        System.out.println("Failed to create table: " + e.getMessage());
        return;
    }
    
}
```

### <a name="data-ingestion"></a>データ インジェスト

既存の Azure Blob Storage コンテナーのファイルを使用してインジェストをキューに登録します。 
* `BlobSourceInfo` を使用して、Blob Storage パスを指定します。
* `IngestionProperties` を使用して、テーブル、データベース、マッピング名、およびデータ型を定義します。 次の例では、データ型は `CSV` です。

```java
    ...
    static final String blobPathFormat = "https://%s.blob.core.windows.net/%s/%s%s";
    static final String blobStorageAccountName = "kustosamplefiles";
    static final String blobStorageContainer = "samplefiles";
    static final String fileName = "StormEvents.csv";
    static final String blobStorageToken = "??st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    ....

    static void ingestFile(String database) throws InterruptedException {
        String blobPath = String.format(blobPathFormat, blobStorageAccountName, blobStorageContainer,
                fileName, blobStorageToken);
        BlobSourceInfo blobSourceInfo = new BlobSourceInfo(blobPath);

        IngestionProperties ingestionProperties = new IngestionProperties(database, tableName);
        ingestionProperties.setDataFormat(DATA_FORMAT.csv);
        ingestionProperties.setIngestionMapping(ingestionMappingRefName, IngestionMappingKind.Csv);
        ingestionProperties.setReportLevel(IngestionReportLevel.FailuresAndSuccesses);
        ingestionProperties.setReportMethod(IngestionReportMethod.QueueAndTable);
    ....
```

インジェスト プロセスは別のスレッドで開始され、`main` スレッドはインジェスト スレッドが完了するまで待機します。 このプロセスでは、[CountdownLatch](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CountDownLatch.html) が使用されます。 インジェスト API (`IngestClient#ingestFromBlob`) は非同期ではありません。 `while` ループを使用して、現在の状態が 5 秒ごとにポーリングされ、インジェストの状態が `Pending` から別の状態に変わるまで待機します。 最終的な状態は、`Succeeded`、`Failed`、または `PartiallySucceeded` です。

```java
        ....
        CountDownLatch ingestionLatch = new CountDownLatch(1);
        new Thread(new Runnable() {
            @Override
            public void run() {
                IngestionResult result = null;
                try {
                    result = getIngestionClient().ingestFromBlob(blobSourceInfo, ingestionProperties);
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
                try {
                    IngestionStatus status = result.getIngestionStatusCollection().get(0);
                    while (status.status == OperationStatus.Pending) {
                        Thread.sleep(5000);
                        status = result.getIngestionStatusCollection().get(0);
                    }
                    ingestionLatch.countDown();
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
            }
        }).start();
        ingestionLatch.await();
    }
```

> [!TIP]
> さまざまなアプリケーションで非同期にインジェストを処理するほかの方法があります。 たとえば、[`CompletableFuture`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) を使用して、テーブルに対してクエリを実行したり、`IngestionStatus` に報告された例外を処理するなど、インジェスト後のアクションを定義するパイプラインを作成できます。

## <a name="run-the-application"></a>アプリケーションの実行

### <a name="general"></a>全般

サンプル コードを実行すると、次のアクションが行われます。

   1. **テーブルを削除する** : `StormEvents` テーブルが削除されます (存在する場合)。
   1. **テーブルの作成** : `StormEvents` テーブルが作成されます。
   1. **マッピングの作成** : `StormEvents_CSV_Mapping` マッピングが作成されます。
   1. **ファイルのインジェスト** : (Azure Blob Storage 内の) CSV ファイルがインジェスト用にキューに登録されます。

次のサンプル コードは、`App.java` からのものです。

```java
public static void main(final String[] args) throws Exception {
    dropTable(database);
    createTable(database);
    createMapping(database);
    ingestFile(database);
}
```

> [!TIP]
> 操作のさまざまな組み合わせを試すには、`App.java` 内の各メソッドをコメント解除したり、コメント化したりします。

### <a name="run-the-application"></a>アプリケーションの実行

1. GitHub から次のサンプル コードをクローンします。

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-java-sdk-ingest.git
    cd azure-data-explorer-java-sdk-ingest
    ```

1. プログラムで使用される環境変数として、次の情報を使用してサービス プリンシパル情報を設定します。
    * クラスター エンドポイント 
    * データベース名 

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. ビルドして実行します。

    ```console
    mvn clean package
    java -jar target/adx-java-ingest-jar-with-dependencies.jar
    ```

    次のように出力されます。

    ```console
    Table dropped
    Table created
    Mapping created
    Waiting for ingestion to complete...
    ```

インジェスト プロセスが完了するまで数分待ちます。 正常に完了すると、`Ingestion completed successfully` というログ メッセージが表示されます。 この時点でプログラムを終了し、既にキューに登録されているインジェスト プロセスに影響を与えずに次の手順に進むことができます。

## <a name="validate"></a>検証

キューに登録されたインジェストでインジェスト プロセスがスケジュールされ、Azure Data Explorer にデータが読み込まれるまで、5 分から 10 分待ちます。 

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインして、クラスターに接続します。 
1. 次のコマンドを実行して、`StormEvents` テーブル内のレコードの数を取得します。
    
    ```kusto
    StormEvents | count
    ```

## <a name="troubleshoot"></a>トラブルシューティング

1. 過去 4 時間以内のインジェスト エラーを表示するには、データベースで次のコマンドを実行します。 

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. 過去 4 時間以内のすべてのインジェスト操作の状態を表示するには、次のコマンドを実行します。

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

先ほど作成したリソースを使用する予定がない場合は、データベースで次のコマンドを実行して、`StormEvents` テーブルを削除します。

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>次のステップ

[クエリを作成する](write-queries.md)
