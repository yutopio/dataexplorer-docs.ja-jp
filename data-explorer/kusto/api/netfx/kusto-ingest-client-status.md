---
title: Kusto.Ingest リファレンス - インジェスト ステータス レポート - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの Kusto.Ingest リファレンス - インジェスト ステータス レポートについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1a3eed1db0ec7d3dd4bc83c0a65f342020b2a387
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523717"
---
# <a name="kustoingest-reference---ingestion-status-reporting"></a>Kusto.Ingest リファレンス - インジェストステータスレポート
この記事では[、IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)機能を使用してインジェスト要求の状態を追跡する方法について説明します。

## <a name="sourcedescription-datareaderdescription-streamdescription-filedescription-and-blobdescription"></a>ソース説明、データリーダー説明、ストリーム説明、ファイル説明、および BLOB の説明
これらのさまざまな記述クラスは、取り込まれるソースデータに関する重要な詳細をカプセル化し、インジェスト操作に提供される必要があります。
これらのクラスはすべて抽象クラス`SourceDescription`から派生し、各データ ソースの一意の識別子をインスタンス化するために使用されます。
提供された識別子は、後続の操作ステータスの追跡を目的とし、適切な操作に関連するすべてのレポート、トレース、および例外に表示されます。

### <a name="class-sourcedescription"></a>クラスソース説明
>大きなデータセット(例えば、1GB以上のDataReader)を取り込む場合、データは1GBのチャンクに分割され、個別に取り込まれます。<BR>この場合、同じデータセットから取得されたすべての取り込み操作に同じ SourceId が適用されます。   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>クラス データリーダー説明
```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>クラス ストリーム説明
```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>クラス ファイル説明
```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>クラス BLOB 説明
```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>インジェスレーション結果表現

### <a name="interface-ikustoingestionresult"></a>インターフェイスのイクストインジングの結果
このインターフェイスは、キューに入れ込まれた単一の取り込み操作の結果`SourceId`をキャプチャし、 によって取得できるようにします。
```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>クラスインジェスティションステータス
インジェスタションステータスは、単一のインジェスション操作の完全なステータスをカプセル化します。
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

### <a name="status-enumeration"></a>ステータスの列挙
|値 |意味 |
|------------|------------|
|保留中 |一時。 摂取操作の結果に基づいて、摂取の過程で変更される可能性があります。 |
|成功 |永久。 彼のデータは正常に摂取されました |
|失敗 |永久。 取り込みが失敗しました |
|キューに登録済み |永久。 データは取り込みのためにキューに入れられます |
|スキップ |永久。 データが指定されず、インジェスト操作がスキップされました |
|部分的に成功しました |永久。 データの一部が正常に取り込まれ、一部が失敗しました |

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>インジェストステータスの追跡 (KustoQueuedingestクライアント)
[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)は 'fire and-forget' クライアントです - クライアント側での取り込み操作は、Azure キューにメッセージをポストして終了し、その後クライアント ジョブが完了します。 クライアントユーザーの便宜のために、KustoQueuedIngestクライアントは個々の取り込みステータスを追跡するためのメカニズムを提供します。 これは、高スループットのインジェスト パイプラインでの大量使用を目的としているのではなく、レートが比較的低く、追跡要件が非常に厳しい場合に、"精度" の取り込み用です。

> [!WARNING]
> 大量のデータ ストリームに対するインジェスト要求ごとに肯定通知を有効にする必要があります。これは、基になる xStore リソースに極端な負荷がかかるため、>、インジェストの待機時間が長くなり、クラスターの応答性が完全になる可能性があるためです。


次のプロパティ[(KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)で設定) は、取得の成功または失敗の通知のレベルとトランスポートを制御します。

### <a name="ingestionreportlevel-enumeration"></a>列挙体の作成
```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>列挙体
```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

インジェストの状態を追跡できるようにするには、インジェスト操作を実行する[IKustoQueuedIngest クライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)に次の情報を提供してください。
1.  プロパティ`IngestionReportLevel`をレポートの目的のレベルに設定します ( FailuresOnly (既定値) または FailuresAndSuccesses のいずれかです。
[なし] に設定すると、インジェストの終了時に何も報告されません。
2.  必要`IngestionReportMethod`な - キュー、テーブル、またはその両方を指定します。

使用例は[、Kusto.Ingest の例](kusto-ingest-client-examples.md)ページにあります。

### <a name="ingestion-status-in-azure-table"></a>Azure テーブルでの取り込みステータス
各`IKustoIngestionResult`INGEST 操作から返されるインターフェイスには、インジェストの状態を照会するために使用できる関数が含まれています。
返される`Status``IngestionStatus`オブジェクトのプロパティに特に注意してください。
* `Pending`ソースが取り込みのためにキューに入れられており、まだ更新されていることを示します。この関数を再度使用して、ソースの状態を照会する
* `Succeeded`ソースが正常に取り込まれたことを示します
* `Failed`ソースを取り込むことができなかったことを示します

>ステータスを`Queued`取得すると、 が`IngestionReportMethod`デフォルト値の 「Queue」に残っていることを示します。 これは永続的な状態であり、関数を呼び出し直すと、常に同じ 'キューに入れ' 状態になります。<BR>Azure テーブルでインジェストの状態を確認できるようにするには、取り込む前に`IngestionReportMethod`[、KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)のプロパティがに`Table`設定されていることを確認してください (または`QueueAndTable`、インジェストの状態をキューに報告する場合)。

### <a name="ingestion-status-in-azure-queue"></a>Azure キューでのインジェクションの状態
これらの`IKustoIngestionResult`メソッドは、Azure テーブルの状態を確認する場合にのみ関連します。 Azure キューに報告された状態を照会するには、[次](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)の方法を使用します。

|Method |目的 |
|------------|------------|
|ピークトップインジェスティション失敗 |要求されたメッセージの制限に従って、破棄されていない最も早いインジェスト失敗に関する情報を返す非同期メソッド |
|失敗を取得して廃棄する |要求されたメッセージの制限に従って、破棄されていない最も早いインジェスト失敗を返し、破棄する非同期メソッド |
|成功を収める |要求されたメッセージの制限に従って、破棄されなかった最も早いインジェストの成功を返し、廃棄する非同期メソッド (に設定`IngestionReportLevel`されている場合にのみ関連します)`FailuresAndSuccesses` |


### <a name="ingestion-failures-retrieved-from-azure-queue"></a>Azure キューから取得したインジェクション エラー
取り込みエラーは、障害に`IngestionFailure`関する有用な情報を含むオブジェクトによって表されます。

|プロパティ |意味 |
|------------|------------|
|データベース & テーブル |目的のデータベース名とテーブル名 |
|インジェスティションソースパス |取得した BLOB のパス。 ファイルの取り込み時に元のファイル名が含まれます。 データリーダーの取り込みの場合はランダムになります |
|失敗ステータス |`Permanent`(再試行は実行されません)、(`Transient`再試行が実行されます)、`Exhausted`または(再試行も何度も失敗しました) |
|ルートアクティビティid&操作ID |インジェストの操作 ID と RootActivity ID (詳細なトラブルシューティングに役立ちます) |
|失敗した |失敗の UTC 時刻。 取り込みメソッドが呼び出された時間よりも長くなります。.インジェストを実行する前にデータが集計される |
|詳細 |障害に関するその他の詳細(存在する場合) |
|ErrorCode |`IngestionErrorCode`失敗した場合のインジェスション エラー コードを表す列挙型|