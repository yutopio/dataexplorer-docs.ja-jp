---
title: Kusto. インジェストの状態レポートの取り込み-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーでのインジェストステータスレポートの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 76ae07e2e7bdbb15900385b1e2feab0c9ff97d01
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373627"
---
# <a name="kustoingest-ingestion-status-reporting"></a>Kusto。インジェストステータスレポートの取り込み

この記事では、 [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)機能を使用してインジェスト要求の状態を追跡する方法について説明します。

## <a name="description-classes"></a>Description クラス

これらの説明クラスには、取り込まれたするソースデータに関する重要な詳細情報が含まれており、インジェスト操作で使用する必要があります。 

* SourceDescription
* DataReaderDescription
* StreamDescription
* FileDescription
* BlobDescription

クラスはすべて、抽象クラスから派生 `SourceDescription` し、各データソースの一意の識別子をインスタンス化するために使用されます。 各識別子はステータス追跡に使用され、関連する操作に関連するすべてのレポート、トレース、および例外に表示されます。

### <a name="class-sourcedescription"></a>クラス SourceDescription

大規模なデータセットは 1 GB のチャンクに分割され、各部分は個別に取り込まれたされます。 同じ SourceId は、同じデータセットから生成されたすべての取り込み操作に適用されます。   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>クラス DataReaderDescription

```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>クラス StreamDescription

```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>クラス FileDescription

```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>クラス BlobDescription

```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>インジェストの結果表現

### <a name="interface-ikustoingestionresult"></a>インターフェイス IKustoIngestionResult

このインターフェイスは、1つのキューに格納されたインジェスト操作の結果をキャプチャし、によって取得でき `SourceId` ます。

```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>IngestionStatus クラス

IngestionStatus には、単一のインジェスト操作の完全な状態が含まれています。

```csharp
public class IngestionStatus
{
    // The ingestion status returns from the service. Status remains 'Pending' during the ingestion process and
    // is updated by the service once the ingestion completes. When <see cref="IngestionReportMethod"/> is set to 'Queue' the ingestion status
    // will always be 'Queued' and the caller needs to query the report queues for ingestion status, as configured. To query statuses that were
    // reported to queue, see: <see href="https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-status#ingestion-status-in-azure-queue"/>.
    // When <see cref="IngestionReportMethod"/> is set to 'Table', call <see cref="IKustoIngestionResult.GetIngestionStatusBySourceId"/> or
    // <see cref="IKustoIngestionResult.GetIngestionStatusCollection"/> to retrieve the most recent ingestion status.
    public Status Status { get; set; }
    // A unique identifier representing the ingested source. Can be supplied during the ingestion execution.
    public Guid IngestionSourceId { get; set; }
    // The URI of the blob, potentially including the secret needed to access the blob.
    // This can be a filesystem URI (on-premises deployments only), or an Azure Blob Storage URI (including a SAS key or a semicolon followed by the account key)
    public string IngestionSourcePath { get; set; }
    // The name of the database holding the target table.
    public string Database { get; set; }
    // The name of the target table into which the data will be ingested.
    public string Table { get; set; }
    // The last updated time of the ingestion status.
    public DateTime UpdatedOn { get; set; }
    // The ingestion's operation Id.
    public Guid OperationId { get; set; }
    // The ingestion operation activity Id.
    public Guid ActivityId { get; set; }
    // In case of a failure - indicates the failure's error code.
    public IngestionErrorCode ErrorCode { get; set; }
    // In case of a failure - indicates the failure's status.
    public FailureStatus FailureStatus { get; set; }
    // In case of a failure - indicates the failure's details.
    public string Details { get; set; }
    // In case of a failure - indicates whether or not the failures originate from an Update Policy.
    public bool OriginatesFromUpdatePolicy { get; set; }
}
```

### <a name="status-enumeration"></a>状態の列挙型

|値              |意味                                                                                     |一時的/永続的
|-------------------|-----------------------------------------------------------------------------------------------------|---------|
|Pending            |取り込み操作の結果に基づいて、値がインジェストの過程で変更される可能性があります。 |一時|
|成功          |データが正常に取り込まれたされました                                                              |永続的| 
|Failed             |インジェストに失敗しました                                                                                     |永続的|
|キューに登録済み             |データが取り込みのためにキューに入れられました                                                               |永続的|
|スキップ            |データが指定されませんでした。取り込み操作はスキップされました                                            |永続的|
|PartiallySucceeded |データの一部が正常に取り込まれたましたが、失敗しました                                        |永続的|

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>インジェストステータスの追跡 (KustoQueuedIngestClient)

[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)は、"火災と忘れる" クライアントです。 クライアント側でのインジェスト操作は、Azure キューにメッセージを送信することによって終了します。 ポストされると、クライアントジョブが完了します。 クライアントユーザーの便宜を実現するために、KustoQueuedIngestClient には個々のインジェストステータスを追跡するためのメカニズムが用意されています。 このメカニズムは、高スループットのインジェストパイプラインでの大量使用を想定していません。 このメカニズムは、レートが比較的低く、追跡要件が厳密である場合に、有効桁数のインジェストに使用されます。

> [!WARNING]
> 大量のデータストリームに対するすべてのインジェスト要求に対して肯定通知を有効にすることは避けてください。これにより、基になる xStore リソースに極端な負荷がかかるため、取り込みの待機時間が長くなり、クラスターの応答性が完全でない場合もあります。

次のプロパティ ( [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)で設定) は、インジェストの成功または失敗の通知のレベルとトランスポートを制御します。

### <a name="ingestionreportlevel-enumeration"></a>IngestionReportLevel 列挙型

```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>IngestionReportMethod 列挙型

```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

インジェストの状態を追跡するには、取り込み操作を行う[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)に次の情報を指定します。
1.  `IngestionReportLevel`プロパティをレポートの必要なレベルに設定します。 `FailuresOnly`(既定値) またはのいずれか `FailuresAndSuccesses` です。
に設定すると `None` 、インジェストの最後に何も報告されません。
1.  、、またはを指定し `IngestionReportMethod`  -  `Queue` `Table` `both` ます。

使用例については、「 [Kusto. インジェストの例](kusto-ingest-client-examples.md)」ページを参照してください。

### <a name="ingestion-status-in-the-azure-table"></a>Azure テーブルでのインジェストの状態

`IKustoIngestionResult`各取り込み操作から返されるインターフェイスには、インジェストの状態を照会するために使用できる関数が含まれています。
返されるオブジェクトのプロパティには特に注意して `Status` `IngestionStatus` ください。
* `Pending`ソースがインジェストのためにキューに登録されていて、まだ更新されていないことを示します。 関数を再度使用して、ソースの状態を照会します。
* `Succeeded`ソースが正常に取り込まれたされたことを示します。
* `Failed`ソースを取り込まれたできなかったことを示します。

> [!NOTE]
> 状態を取得する `Queued` と、が `IngestionReportMethod` 既定値の ' Queue ' のままになっていることを示します。 これは永続的な状態であり、または関数を再起動すると `GetIngestionStatusBySourceId` `GetIngestionStatusCollection` 、常に同じ "キューに置かれた" 状態になります。
> Azure テーブルのインジェストの状態を確認するには、取り込みの前に、 `IngestionReportMethod` [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)のプロパティがに設定されていることを確認し `Table` ます。 また、インジェストの状態をキューに報告する場合は、[状態] をに設定し `QueueAndTable` ます。

### <a name="ingestion-status-in-azure-queue"></a>Azure キューでのインジェストの状態

メソッドは、 `IKustoIngestionResult` Azure テーブルの状態を確認する場合にのみ関連します。 Azure キューに報告された状態を照会するには、次の[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)メソッドを使用します。

|Method                                  |目的     |
|----------------------------------------|------------|
|PeekTopIngestionFailures                |要求されたメッセージの制限により、まだ破棄されていない最も古いインジェストエラーに関する情報を返す非同期メソッド |
|GetAndDiscardTopIngestionFailures       |要求されたメッセージの制限により、まだ破棄されていない最も古いインジェストエラーを返して破棄する非同期メソッド |
|GetAndDiscardTopIngestionSuccesses      |要求されたメッセージの制限により、まだ破棄されていない最も古いインジェストの成功を返して破棄する非同期メソッド。 このメソッドは、 `IngestionReportLevel` がに設定されている場合にのみ関連します。`FailuresAndSuccesses` |

### <a name="ingestion-failures-retrieved-from-the-azure-queue"></a>Azure キューからインジェストのエラーが取得されました

インジェストエラーは、 `IngestionFailure` エラーに関する有用な情報を含むオブジェクトによって表されます。

|プロパティ                      |説明     |
|------------------------------|------------|
|データベース & テーブル              |目的のデータベース名とテーブル名 |
|IngestionSourcePath           |取り込まれた blob のパス。 ファイルが取り込まれたの場合、には元のファイル名が含まれます。 DataReader が取り込まれたの場合はランダムになります |
|FailureStatus                 |`Permanent`(再試行は実行されません)、 `Transient` (再試行が実行されます)、または `Exhausted` (いくつかの再試行も失敗) |
|OperationId & RootActivityId  |インジェストの操作 ID と RootActivity ID (詳細なトラブルシューティングに役立ちます) |
|失敗した場合                      |エラーの UTC 時刻。 インジェストを実行する前にデータが集計されるため、インジェストメソッドが呼び出された時間よりも長くなります。 |
|詳細                       |エラーに関するその他の詳細 (存在する場合) |
|ErrorCode                     |`IngestionErrorCode`列挙は、エラーが発生した場合にインジェストエラーコードを表します。 |
