---
title: Kusto. インジェストの参照-インジェストコードの例-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Kusto. インジェストのコード例について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/15/2019
ms.openlocfilehash: ba3232ca1c8a3f587f53ee1c3c6aad3fc12283ad
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799681"
---
# <a name="kustoingest-reference---ingestion-code-examples"></a>Kusto. インジェストリファレンス-インジェストコードの例
これは、Kusto テーブルにデータを取り込みするさまざまな手法を示す短いコードスニペットのコレクションです。

>リマインダー: これらのサンプルは、インジェストの後すぐにインジェストクライアントが破棄されるかのように見えます。 これを文字どおりに実行しないでください。<BR>インジェストクライアントは再入可能で、スレッドセーフであり、大きな数値で作成することはできません。 インジェストクライアントインスタンスの推奨カーディナリティは、ターゲット Kusto クラスターごとのホストプロセスごとに1つです。

### <a name="useful-references"></a>参考資料
* [Kusto. インジェストクライアントリファレンス](kusto-ingest-client-reference.md)
* [Kusto. 取り込み操作の状態](kusto-ingest-client-errors.md)
* [Kusto. インジェスト例外](kusto-ingest-client-errors.md)
* [Kusto の接続文字列](../connection-strings/kusto.md)
* [Kusto 承認モデル](../../management/security-roles.md)

### <a name="async-ingestion-from-a-single-azure-blob-using-kustoqueuedingestclient-with-optional-retrypolicy"></a>KustoQueuedIngestClient を使用して単一の Azure Blob から非同期インジェストを実行する (省略可能) RetryPolicy:
```csharp
//Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create an ingest client
// Note, that creating a separate instance per ingestion operation is an anti-pattern.
// IngestClient classes are thread-safe and intended for reuse
IKustoIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from blobs according to the required properties
var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

var sourceOptions = new StorageSourceOptions() { DeleteSourceOnSuccess = true };

//// Create your custom implementation of IRetryPolicy, which will affect how the ingest client handles retrying on transient failures
IRetryPolicy retryPolicy = new NoRetry();
//// This line sets the retry policy on the ingest client that will be enforced on every ingest call from here on
((IKustoQueuedIngestClient)client).QueueRetryPolicy = retryPolicy;

await client.IngestFromStorageAsync(uri: @"BLOB-URI-WITH-SAS-KEY", ingestionProperties: kustoIngestionProperties, sourceOptions);

client.Dispose();
```

### <a name="ingest-from-local-file-using-kustodirectingestclient"></a>KustoDirectIngestClient を使用したローカルファイルからの取り込み 

この方法は、制限されたボリュームと低頻度のインジェストの場合にお勧めします。

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderEngine =
    new KustoConnectionStringBuilder(@"https://{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
using (IKustoIngestClient client = KustoIngestFactory.CreateDirectIngestClient(kustoConnectionStringBuilderEngine))
{
    //Ingest from blobs according to the required properties
    var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

    client.IngestFromStorageAsync(@"< Path to local file >", ingestionProperties: kustoIngestionProperties).GetAwaiter().GetResult();
}
```

### <a name="ingest-from-local-files-using-kustoqueuedingestclient-and-ingestion-validation"></a>KustoQueuedIngestClient およびインジェスト検証を使用したローカルファイルからの取り込み 
```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from files according to the required properties
var kustoIngestionProperties = new KustoIngestionProperties(databaseName: "myDB", tableName: "myTable");

client.IngestFromStorageAsync(@"ValidTestFile.csv", kustoIngestionProperties);
client.IngestFromStorageAsync(@"InvalidTestFile.csv", kustoIngestionProperties);

// Waiting for the aggregation
Thread.Sleep(TimeSpan.FromMinutes(8));

// Retrieve and validate failures
var ingestionFailures = client.PeekTopIngestionFailures().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "Failures expected");
// Retrieve, delete and validate failures
ingestionFailures = client.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "Failures expected");

// Dispose of the client
client.Dispose();
```

### <a name="ingest-from-a-local-files-using-kustoqueuedingestclient-and-report-status-to-a-queue"></a>KustoQueuedIngestClient を使用してローカルファイルから取り込み、キューに状態を報告する

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from a file according to the required properties
var kustoIngestionProperties = new KustoQueuedIngestionProperties(databaseName: "myDB", tableName: "myTable")
{
    // Setting the report level to FailuresAndSuccesses will cause both successful and failed ingestions to be reported
    // (Rather than the default "FailuresOnly" level - which is demonstrated in the
    // 'Ingest From Local File(s) using KustoQueuedIngestClient and Ingestion Validation' section)
    ReportLevel = IngestionReportLevel.FailuresAndSuccesses,
    // Choose the report method of choice. 'Queue' is the default method.
    // For the sake of the example, we will choose it anyway. 
    ReportMethod = IngestionReportMethod.Queue
};

client.IngestFromStorageAsync("ValidTestFile.csv", kustoIngestionProperties);
client.IngestFromStorageAsync("InvalidTestFile.csv", kustoIngestionProperties);

// Waiting for the aggregation
Thread.Sleep(TimeSpan.FromMinutes(8));

// Retrieve and validate failures
var ingestionFailures = client.PeekTopIngestionFailures().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "The failed ingestion should have been reported to the failed ingestions queue");
// Retrieve, delete and validate failures
ingestionFailures = client.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
Ensure.IsTrue((ingestionFailures.Count() > 0), "The failed ingestion should have been reported to the failed ingestions queue");

// Verify the success has also been reported to the queue
var ingestionSuccesses = client.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();
Ensure.ConditionIsMet((ingestionSuccesses.Count() > 0),
    "The successful ingestion should have been reported to the successful ingestions queue");

// Dispose of the client
client.Dispose();
```

### <a name="ingest-from-a-local-file-using-kustoqueuedingestclient-and-report-status-to-a-table"></a>KustoQueuedIngestClient を使用してローカルファイルから取り込み、テーブルに状態を報告する

```csharp
// Create Kusto connection string with App Authentication
var kustoConnectionStringBuilderDM =
    new KustoConnectionStringBuilder(@"https://ingest-{clusterNameAndRegion}.kusto.windows.net").WithAadApplicationKeyAuthentication(
        applicationClientId: "{Application Client ID}",
        applicationKey: "{Application Key (secret)}",
        authority: "{AAD TenantID or name}");

// Create a disposable client that will execute the ingestion
IKustoQueuedIngestClient client = KustoIngestFactory.CreateQueuedIngestClient(kustoConnectionStringBuilderDM);

// Ingest from a file according to the required properties
var kustoIngestionProperties = new KustoQueuedIngestionProperties(databaseName: "myDB", tableName: "myDB")
{
    // Setting the report level to FailuresAndSuccesses will cause both successful and failed ingestions to be reported
    // (Rather than the default "FailuresOnly" level)
    ReportLevel = IngestionReportLevel.FailuresAndSuccesses,
    // Choose the report method of choice
    ReportMethod = IngestionReportMethod.Table
};

var filePath = @"< Path to file >";
var fileIdentifier = Guid.NewGuid();
var fileDescription = new FileDescription() { FilePath = filePath, SourceId = fileIdentifier };
var sourceOptions = new StorageSourceOptions() { SourceId = fileDescription.SourceId.Value };

// Execute the ingest operation and save the result.
var clientResult = await client.IngestFromStorageAsync(fileDescription.FilePath,
    ingestionProperties: kustoIngestionProperties, sourceOptions);

// Use the fileIdentifier you supplied to get the status of your ingestion 
var ingestionStatus = clientResult.GetIngestionStatusBySourceId(fileIdentifier);
while (ingestionStatus.Status == Status.Pending)
{
    // Wait a minute...
    Thread.Sleep(TimeSpan.FromMinutes(1));
    // Try again
    ingestionStatus = clientResult.GetIngestionStatusBySourceId(fileIdentifier);
}

// Verify the results of the ingestion
Ensure.ConditionIsMet(ingestionStatus.Status == Status.Succeeded,
    "The file should have been ingested successfully");

// Dispose of the client
client.Dispose();
```