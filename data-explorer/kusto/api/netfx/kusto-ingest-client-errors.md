---
title: Kusto. インジェストエラー & 例外-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Kusto. インジェスト-エラーと例外について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a4da1c35d34a1677cc0608fdf70dcbdb5a3c7c73
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774607"
---
# <a name="kustoingest-errors-and-exceptions"></a>Kusto. 取り込みエラーと例外
クライアント側でのインジェスト処理中にエラーが発生した場合は、C# の例外によって示されます。

## <a name="failures"></a>エラー

### <a name="kustodirectingestclient-exceptions"></a>KustoDirectIngestClient の例外

複数のソースから取り込みを試行しているときに、インジェスト処理中にエラーが発生することがあります。 いずれかのソースのインジェストが失敗した場合はログに記録され、クライアントは残りのソースの取り込みを続行します。 すべてのソースを取り込むと、メンバーを `IngestClientAggregateException` 含むがスローされ `IList<IngestClientException> IngestionErrors` ます。

`IngestClientException` およびその派生クラスには、フィールドとフィールドが含まれて `IngestionSource` `Error` います。 2つのフィールドの組み合わせによって、インジェストに失敗したソースからインジェストの試行中に発生したエラーまでのマッピングが作成されます。 この情報を一覧で使用して、 `IngestionErrors` インジェストに失敗したソースとその理由を調べることができます。 この例外には、 `IngestClientAggregateException` `GlobalError` すべてのソースでエラーが発生したかどうかを示すブール型プロパティも含まれています。

### <a name="failures-ingesting-from-files-or-blobs"></a>ファイルまたは blob からのエラーの取り込み

Blob またはファイルから取り込み中に取り込みエラーが発生した場合、 `deleteSourceOnSuccess` フラグがに設定されていても、インジェストソースは削除されません `true` 。 ソースは、詳細な分析のために保持されます。 エラーの発生元が認識され、エラーがインジェストソース自体から発生しなかった場合、クライアントのユーザーがそのエラーを再取り込みしようとすることがあります。

### <a name="failures-ingesting-from-idatareader"></a>IDataReader からのエラー取り込み

DataReader から取り込みしている間、取り込むデータは既定の場所がである一時フォルダーに保存され `<Temp Path>\Ingestions_<current date and time>` ます。 この既定のフォルダーは、インジェストが正常に完了した後、常に削除されます。

`IngestFromDataReader`メソッドとメソッドでは、既定値がであるフラグによって、 `IngestFromDataReaderAsync` インジェストの `retainCsvOnFailure` `false` 失敗後にファイルを保持するかどうかが決定されます。 このフラグがに設定されている場合 `false` 、インジェストに失敗したデータは保存されず、問題が発生した原因を把握することが難しくなります。

## <a name="kustoqueuedingestclient-exceptions"></a>KustoQueuedIngestClient の例外

`KustoQueuedIngestClient` Azure キューにメッセージをアップロードしてデータを取り込みします。 キューのプロセスの前または中にエラーが発生した場合は、 `IngestClientAggregateException` プロセスの終了時にがスローされます。 スローされる例外には、のコレクションが含まれます。このコレクションには `IngestClientException` 、各エラーのソースが含まれ、キューにポストされていません。 メッセージを投稿しようとしたときに発生したエラーもスローされます。

### <a name="posting-to-queue-failures-with-a-file-or-blob-as-a-source"></a>ソースとしてのファイルまたは blob を使用したキューエラーへの投稿

のメソッドを使用しているときにエラーが発生した場合、 `KustoQueuedIngestClient` `IngestFromFile/IngestFromBlob` `deleteSourceOnSuccess` フラグがに設定されていても、ソースは削除されません `true` 。 代わりに、詳細な分析のためにソースが保持されます。 

エラーの発生元が認識され、エラーがインジェストソース自体から発生しなかった場合、クライアントのユーザーは、失敗したソースと関連するメソッドを使用してデータのキューへを試みることができ `IngestFromFile/IngestFromBlob` ます。 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>ソースとしての IDataReader を使用したキューエラーへの投稿

DataReader ソースを使用している間、キューにポストするデータは、既定の場所がである一時フォルダーに保存され `<Temp Path>\Ingestions_<current date and time>` ます。 このフォルダーは、データが正常にキューに投稿された後、常に削除されます。
`IngestFromDataReader`メソッドとメソッドでは、既定値がであるフラグによって、 `IngestFromDataReaderAsync` インジェストの `retainCsvOnFailure` `false` 失敗後にファイルを保持するかどうかが決定されます。 このフラグがに設定されている場合 `false` 、インジェストに失敗したデータは保存されず、問題が発生した原因を把握することが難しくなります。

### <a name="common-failures"></a>一般的なエラー
|エラー                         |理由           |対応策                                   |
|------------------------------|-----------------|---------------------------------------------|
|データベース <database name> 名が存在しません| データベースが存在しません|データベース名を確認し `kustoIngestionProperties` てください。 |
|種類 ' Table ' のエンティティ ' テーブル名が存在しません。|このテーブルは存在しません。 CSV マッピングはありません。| CSV マッピングを追加する/必要なテーブルを作成する |
|<blob path>理由により除外された Blob: JSON パターンは jsonMapping パラメーターで取り込まれたである必要があります| Json のマッピングが指定されていない場合の JSON インジェスト。|JSON マッピングを指定する |
|Blob をダウンロードできませんでした: ' リモートサーバーがエラーを返しました: (404) が見つかりません。 '| BLOB が存在しません。|Blob が存在することを確認します。 存在する場合は、もう一度お試しください。 Kusto チームにお問い合わせください |
|JSON 列マッピングが無効です: 2 つ以上のマッピング要素が同じ列をポイントしています。| 異なるパスを持つ2つの列を含む JSON マッピング|JSON マッピングの修正 |
|EngineError-[UtilsException] `IngestionDownloader.Download` : 1 つ以上のファイルをダウンロードできませんでした (ActivityID の検索 KustoLogs: <GUID1> 、rootactivityid: <GUID2> )| 1つ以上のファイルをダウンロードできませんでした。 |再試行 |
|解析できませんでした: ID ' ' のストリームの <stream name> CSV 形式が正しくありません。 ValidationOptions ポリシーごとに失敗します |CSV ファイルの形式が正しくありません (たとえば、すべての行に同じ数の列がありません)。 検証ポリシーがに設定されている場合にのみ失敗し `ValidationOptions` ます。 ValidateCsvInputConstantColumns |CSV ファイルを確認します。 このメッセージは、CSV/TSV ファイルにのみ適用されます。 |
|`IngestClientAggregateException` エラーメッセージ ' 有効な Shared Access Signature の必須パラメーターがありません |使用されている SAS はサービスであり、ストレージアカウントではありません。 |ストレージアカウントの SAS を使用する |

### <a name="ingestion-error-codes"></a>インジェストエラーコード

プログラムによるインジェストエラーの処理を支援するために、エラー情報が数値エラーコード () で拡充されてい `IngestionErrorCode enumeration` ます。

インジェストエラーコードの完全な一覧については、「 [Azure データエクスプローラーでのエラーコードの取り込み](../../../error-codes.md)」を参照してください。

## <a name="detailed-exceptions-reference"></a>詳細な例外のリファレンス

### <a name="cloudqueuesnotfoundexception"></a>CloudQueuesNotFoundException

データ管理クラスターからキューが返されなかった場合に発生します

基底クラス: [Exception](/dotnet/api/system.exception)

|フィールド名 |Type     |説明
|-----------|---------|------------------------------|
|エラー      | String  | DM からキューを取得しようとしたときに発生したエラー
                            
[Kusto キュー取り込みクライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)を使用する場合にのみ関連します。
インジェスト処理中に、DM にリンクされている Azure キューを取得しようとする試行がいくつか行われます。 これらの試行が失敗した場合、エラーの理由を含む例外が "エラー" フィールドに発生します。 場合によっては、"InnerException" フィールドの内部例外も発生する可能性があります。


### <a name="cloudblobcontainersnotfoundexception"></a>CloudBlobContainersNotFoundException

データ管理クラスターから blob コンテナーが返されなかった場合に発生します

基底クラス: [Exception](/dotnet/api/system.exception)

|フィールド名   |Type     |説明       
|-------------|---------|------------------------------|
|KustoEndpoint| String  | 関連する DM のエンドポイント
                            
[Kusto キュー取り込みクライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)を使用する場合にのみ関連します。  
ファイル、DataReader、Stream など、Azure コンテナーにまだ存在しないソースを取り込みした場合、データはインジェストのために一時的な blob にアップロードされます。 この例外は、データのアップロード先となるコンテナーが見つからない場合に発生します。

### <a name="duplicateingestionpropertyexception"></a>DuplicateIngestionPropertyException

インジェストプロパティが複数回構成されている場合に発生します

基底クラス: [Exception](/dotnet/api/system.exception)

|フィールド名   |Type     |説明       
|-------------|---------|------------------------------------|
|PropertyName | String  | 重複するプロパティの名前
                            
### <a name="postmessagetoqueuefailedexception"></a>PostMessageToQueueFailedException

メッセージをキューに投稿できなかった場合に発生します

基底クラス: [Exception](/dotnet/api/system.exception)

|フィールド名   |Type     |説明       
|-------------|---------|---------------------------------|
|QueueUri     | String  | キューの URI
|エラー        | String  | キューへの投稿を試行しているときに生成されたエラーメッセージ
                            
[Kusto キュー取り込みクライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)を使用する場合にのみ関連します。  
キューに置かれた取り込みクライアントは、関連する Azure キューにメッセージをアップロードしてデータを取り込みします。 Post エラーが発生した場合は、例外が発生します。 これには、キューの URI、エラーの理由 ("エラー" フィールド)、および "InnerException" フィールドの内部例外が含まれます。

### <a name="dataformatnotspecifiedexception"></a>DataFormatNotSpecifiedException

データ形式が必要ですが、では指定されていない場合に発生します。 `IngestionProperties`

基本クラス: IngestClientException

ストリームから取り込みする場合は、データを適切に取り込むために、 [IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties)でデータ形式を指定する必要があります。 この例外は、が指定されていない場合に発生し `IngestionProperties.Format` ます。

### <a name="invaliduriingestclientexception"></a>InvalidUriIngestClientException

無効な blob URI がインジェストソースとして送信されたときに発生します

基本クラス: IngestClientException

### <a name="compressfileingestclientexception"></a>CompressFileIngestClientException

インジェスト用に指定されたファイルの取り込みクライアントが圧縮に失敗したときに発生します

基本クラス: IngestClientException

ファイルはインジェスト前に圧縮されます。 この例外は、ファイルを圧縮しようとして失敗した場合に発生します。

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

インジェスト用に提供されたソースをインジェストクライアントがアップロードに失敗した場合に発生します。

基本クラス: IngestClientException

### <a name="sizelimitexceededingestclientexception"></a>SizeLimitExceededIngestClientException

インジェストソースが大きすぎる場合に発生します

基本クラス: IngestClientException

|フィールド名   |Type     |説明       
|-------------|---------|-----------------------|
|サイズ         | long    | インジェストソースのサイズ
|MaxSize      | long    | インジェストに許可される最大サイズ

インジェストソースが最大サイズの 4 GB を超えると、例外がスローされます。 サイズの検証は、IngestionProperties クラスのフラグによってオーバーライドでき `IgnoreSizeLimit` ます。 [IngestionProperties class](kusto-ingest-client-reference.md#class-kustoingestionproperties) ただし、1 GB を超える 1 [つのソースを](about-kusto-ingest.md#ingestion-best-practices)取り込むことは推奨されません。

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

インジェスト用に提供されたファイルをインジェストクライアントが一時 blob にアップロードできなかった場合に発生します

基本クラス: IngestClientException

### <a name="directingestclientexception"></a>DirectIngestClientException

直接インジェストの実行中に一般的なエラーが発生した場合に発生します

基本クラス: IngestClientException

### <a name="queuedingestclientexception"></a>QueuedIngestClientException

キューに置かれたインジェストの実行中にエラーが発生した場合に発生します

基本クラス: IngestClientException

### <a name="ingestclientaggregateexception"></a>IngestClientAggregateException

インジェスト中に1つ以上のエラーが発生した場合に発生します

基本クラス: [AggregateException](/dotnet/api/system.aggregateexception)

|フィールド名      |Type                             |説明       
|----------------|---------------------------------|-----------------------|
|IngestionErrors | IList<IngestClientException>    | 取り込みの試行中に発生したエラーと、それらに関連するソース
|IsGlobalError   | [bool]                            | すべてのソースで例外が発生したかどうかを示します


