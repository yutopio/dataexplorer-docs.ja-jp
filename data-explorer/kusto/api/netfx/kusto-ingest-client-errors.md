---
title: Kusto.Ingest リファレンス - エラーと例外 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの Kusto.Ingest リファレンス - エラーと例外について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f8f50322a79dea8890b4a4ad5eaa78f0b8fe3bc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524346"
---
# <a name="kustoingest-reference---errors-and-exceptions"></a>Kusto.Ingest リファレンス - エラーと例外
クライアント側での取り込み処理中のエラーは、C# 例外を介してユーザー コードに公開されます。

## <a name="failures-overview"></a>障害の概要

### <a name="kustodirectingestclient-exceptions"></a>クストダイレクトインジェストクライアントの例外
複数のソースから取り込もうとしている間に、それらのソースの一部の取り込み中にエラーが発生する可能性がありますが、他のソースは正常に摂取される可能性があります。 特定のソースに対してインジェストが失敗した場合、その取り込みがログに記録され、クライアントは取り込み用の残りのソースを引き続き取り込みます。 インジェストのすべてのソースを調けた後、メンバー`IngestClientAggregateException``IList<IngestClientException> IngestionErrors`を含む をスローします。
`IngestClientException`その派生クラスには、取り`IngestionSource`込みに`Error`失敗したソースから、取得中に発生したエラーへのマッピングを形成するフィールドとフィールドが含まれます。 インジェストエラーリストの情報を使用して、取り込まれなかったソースとその理由を調べることができます。 `IngestClientAggregateException`例外には、すべてのソースで`GlobalError`エラーが発生したかどうかを示すブール型プロパティも含まれます。

### <a name="failures-ingesting-from-files-or-blobs"></a>ファイルまたは BLOB からの取り込みエラー 
BLOB\file からの取り込み中にインジェスト エラーが発生した場合、`deleteSourceOnSuccess`フラグが に`true`設定されていても、インジェスト ソースは削除されません。
ソースは、さらなる分析のために保存されます。 エラーの発生元を理解し、そのエラーがインジェストソース自体から発生していないことを考えると、クライアントのユーザーは再び取り込もうとします。

### <a name="failures-ingesting-from-idatareader"></a>IDataReader からの取り込みエラー
DataReader から取り込む間、取り込むデータは、既定の場所が`<Temp Path>\Ingestions_<current date and time>`の一時フォルダーに保存されます。 このフォルダは、正常に取り込んだ後、常に削除されます。<BR>
`IngestFromDataReader`メソッド`IngestFromDataReaderAsync`とメソッドでは、フラグ`retainCsvOnFailure`(既定値は )`false`によって、取り込みが失敗した後にファイルを保持するかどうかを決定します。 このフラグを に`false`設定すると、インジェストに失敗したデータは保持されず、何が間違っていたのかを理解するのが難しくなり、

## <a name="kustoqueuedingestclient-exceptions"></a>クストキューディングクライアントの例外
メッセージを Azure キューにアップロードしてデータを取り込みます。 キューイング プロセスの前および処理中にエラー`IngestClientAggregateException`が発生した場合、キューにポストされなかったソース (失敗ごとに)`IngestClientException`とメッセージのポスト中に発生したエラーを含むコレクションを含むコレクションを使用して、実行の最後にスローされます。

### <a name="posting-to-queue-failures-with-file-or-blob-as-a-source"></a>ファイルまたは BLOB をソースとしてキューの失敗にポストする
KustoQueuedIngestClient の IngestFromFile/IngestFromBlob メソッドの使用中にエラーが発生した場合、`deleteSourceOnSuccess`フラグが に`true`設定されていてもソースは削除されず、詳細な分析のために保存されます。 エラーの発生元を理解し、エラーがソース自体から発生していないことを考えると、クライアントのユーザーは、失敗したソースで関連する IngestFromFile/IngestFromBlob メソッドを使用してデータを再キューに入れようとする可能性があります。 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>ソースとしての IDataReader を使用したキューの失敗への投稿
DataReader ソースを使用している間、キューにポストするデータは、既定の場所が`<Temp Path>\Ingestions_<current date and time>`に設定されている一時フォルダーに保存されます。
このフォルダは、データがキューに正常にポストされた後、常に削除されます。
`IngestFromDataReader`メソッド`IngestFromDataReaderAsync`とメソッドでは、フラグ`retainCsvOnFailure`(既定値は )`false`によって、取り込みが失敗した後にファイルを保持するかどうかを決定します。 このフラグを に`false`設定すると、インジェストに失敗したデータは保持されず、何が間違っていたのかを理解するのが難しくなり、

### <a name="common-failures"></a>一般的な障害
|Error|理由|対応策|
|------------------------------|----|------------|
|データベース<database name>名が存在しません| データベースが存在しません|データベース名を確認する |
|'Table' の種類のエンティティ '存在しないテーブル名' が見つかりませんでした。|テーブルが存在せず、CSV マッピングもありません。| CSV マッピングの追加/必要なテーブルの作成 |
|理由<blob path>から除外された BLOB: json パターンは jsonMapping パラメーターで取り込む必要があります| json マッピングが指定されていない場合の Json インジェスティション。|JSON マッピングの提供 |
|BLOB のダウンロードに失敗しました: 'リモート サーバーがエラーを返しました: (404) 見つかりません。| BLOB が存在しません。|BLOB が存在することを確認し、存在する場合は再試行し、Kusto チームに連絡してください。 |
|Json 列マッピングが無効です: 2 つ以上のマッピング要素が同じ列を指しています。| JSON マッピングには、異なるパスを持つ 2 つの列があります。|JSON マッピングを修正 |
|エンジンエラー - [UtilsException] インジェスティオンダウンローダー.ダウンロード:1つ以上のファイルをダウンロードできませんでした(アクティビティIDのKustoLogsを<GUID1>検索<GUID2>:、ルートアクティビティID:)| 1 つ以上のファイルをダウンロードできませんでした。 |[再試行] |
|解析に失敗しました: id<stream name>' ' のストリームの形式が不正な Csv 形式で、検証ごとの失敗オプション ポリシー |不正な形式の csv ファイル (各行の列数が同じではないなど)。 検証ポリシーが [検証オプション] に設定されている場合にのみ失敗します。 列を検証します。 |csv ファイルを確認します。 このメッセージは、csv/tsv ファイルにのみ適用されます |
|エラー メッセージ '有効な共有アクセス 署名の必須パラメーターがありません' を持つ IngestClientAggregateException |使用している SAS はサービスであり、ストレージ アカウントのサービスではありません。 |ストレージ アカウントの SAS を使用する |

### <a name="ingestion-error-codes"></a>インジェクション エラー コード

インジェストエラーをプログラムで処理するために、エラー情報は数値エラー コード (IngestionErrorCode 列挙) で強化されます。

|ErrorCode|理由|
|-----------|-------|
|Unknown| 不明なエラーが発生しました|
|Stream_LowMemoryCondition| 操作がメモリ不足|
|Stream_WrongNumberOfFields| CSV ドキュメントのフィールド数が一致しません|
|Stream_InputStreamTooLarge| 取り込みのために提出されたドキュメントが許容サイズを超えました|
|Stream_NoDataToIngest| 取り込むデータ ストリームが見つかりません|
|Stream_DynamicPropertyBagTooLarge| 取り込まれたデータの動的列の 1 つに、一意のプロパティが多すぎます。|
|Download_SourceNotFound| Azure ストレージからソースをダウンロードできませんでした - ソースが見つかりません|
|Download_AccessConditionNotSatisfied| Azure ストレージからソースをダウンロードできませんでした - アクセスが拒否されました|
|Download_Forbidden| Azure ストレージからソースをダウンロードできませんでした - アクセスは許可されていません|
|Download_AccountNotFound| Azure ストレージからソースをダウンロードできませんでした - アカウントが見つかりません|
|Download_BadRequest| Azure ストレージからソースをダウンロードできませんでした - 要求が正しくありません|
|Download_NotTransient| Azure ストレージからソースをダウンロードできませんでした - 一時的なエラーではありません|
|Download_UnknownError| Azure ストレージからソースをダウンロードできませんでした - 不明なエラー|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| 更新ポリシーを呼び出すことができませんでした。 クエリ スキーマがテーブル スキーマと一致しません|
|UpdatePolicy_FailedDescendantTransaction| 更新ポリシーを呼び出すことができませんでした。 失敗した下位トランザクション更新ポリシー|
|UpdatePolicy_IngestionError| 更新ポリシーを呼び出すことができませんでした。 取り込みエラーが発生しました|
|UpdatePolicy_UnknownError| 更新ポリシーを呼び出すことができませんでした。 不明なエラーが発生しました|
|BadRequest_MissingJsonMappingtFailure| json パターンが jsonMapping パラメーターで取り込まれませんでした|
|BadRequest_InvalidOrEmptyBlob| BLOB が無効または空の zip アーカイブです|
|BadRequest_DatabaseNotExist| データベースが存在しません|
|BadRequest_TableNotExist| テーブルが存在しません|
|BadRequest_InvalidKustoIdentityToken| 無効な kusto ID トークン|
|BadRequest_UriMissingSas| 不明な BLOB ストレージからの SAS を使用しない BLOB パス|
|BadRequest_FileTooLarge| ファイルを取り込もうとしている|
|BadRequest_NoValidResponseFromEngine| インジェスト・コマンドからの有効な応答がありません|
|BadRequest_TableAccessDenied| テーブルへのアクセスが拒否されました|
|BadRequest_MessageExhausted| メッセージが使い果たされました|
|General_BadRequest| 一般的な不正要求 (既存ではないデータベース/テーブルへの取り込みが必要になる可能性があります)|
|General_InternalServerError| 内部サーバー エラーが発生しました|

## <a name="detailed-kustoingest-exceptions-reference"></a>詳細なクストー.インジェスト例外リファレンス

### <a name="cloudqueuesnotfoundexception"></a>クラウドキューは見つかりませんでした。
データ管理クラスターからキューが返されなかった場合に発生します。

基本クラス:[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

フィールド:

|名前|Type|意味
|-----------|----|------------------------------|
|Error| `String`| DM からキューを取得しようとしたときに発生したエラー
                            
追加情報:

[Kusto キューイングインジェスト クライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)を使用する場合にのみ関連します。
取り込みプロセス中に、DM にリンクされた Azure キューを取得する試みが何度か行われます。 これらの試行が失敗すると、'Error' フィールドにエラーの理由を含む例外が発生し、場合によっては 'InnerException' フィールド内の内部例外が発生します。


### <a name="cloudblobcontainersnotfoundexception"></a>見つかりませんでした。
BLOB コンテナーがデータ管理クラスターから返されなかった場合に発生します。

基本クラス:[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

フィールド:

|名前|Type|意味       
|-----------|----|------------------------------|
|クストエンドポイント| `String`| 関連する DM のエンドポイント
                            
追加情報:

[Kusto キューイングインジェスト クライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)を使用する場合にのみ関連します。  
Azure コンテナーにまだ存在しないソース (ファイル、DataReader、または Stream) を取得する場合、データはインジェスト用に一時的な BLOB にアップロードされます。 例外は、データのアップロードするコンテナが見つからない場合に発生します。

### <a name="duplicateingestionpropertyexception"></a>プロパティの重複例外
インジェスト プロパティが複数回設定されている場合に発生します。

基本クラス:[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

フィールド:

|名前|Type|意味       
|-----------|----|------------------------------|
|PropertyName| `String`| 重複するプロパティの名前
                            
### <a name="postmessagetoqueuefailedexception"></a>イベントメッセージを送信します。
キューへのメッセージの投稿に失敗したときに発生します

基本クラス:[例外](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

フィールド:

|名前|Type|意味       
|-----------|----|------------------------------|
|キューUri| `String`| キューの URI
|Error| `String`| キューへのポストの試行中に生成されたエラー メッセージ
                            
追加情報:

[Kusto キューイングインジェスト クライアント](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)を使用する場合にのみ関連します。  
キューに入っている INGEST クライアントは、関連する Azure キューにメッセージをアップロードしてデータを取得します。 ポストエラーの場合、キュー URI、エラーの理由、および 'InnerException' フィールド内の内部例外を含む例外が発生します。

### <a name="dataformatnotspecifiedexception"></a>指定された例外
データ形式が必要だが、インジェストプロパティで指定されていない場合に発生します。

基本クラス: インジェストクライアント例外

追加情報:

ストリームから取り込む場合、データを適切に取り込むには、[インジェストプロパティ](kusto-ingest-client-reference.md#class-kustoingestionproperties)でデータ形式を指定する必要があります。 この例外は、インジェストプロパティ.フォーマットが指定されていない場合に発生します。

### <a name="invaliduriingestclientexception"></a>無効なUriIngestクライアント例外
無効な BLOB URI がインジェス ソースとして送信されたときに発生します

基本クラス: インジェストクライアント例外

### <a name="compressfileingestclientexception"></a>ファイルイングエストクライアント例外を圧縮する
取り込み用に提供されたファイルを、INGEST クライアントが圧縮できなかった場合に発生します。

基本クラス: インジェストクライアント例外

追加情報:

ファイルは、取り込む前に圧縮されます。 この例外は、ファイルの圧縮に失敗した場合に発生します。

### <a name="uploadfiletotempblobingestclientexception"></a>クライアント例外を処理します。
インジェスト クライアントが一時的な BLOB への取り込み用に提供されたソースのアップロードに失敗したときに発生します。

基本クラス: インジェストクライアント例外

### <a name="sizelimitexceededingestclientexception"></a>クライアント例外を超えました。
インジェスト ソースが大きすぎる場合に発生します。

基本クラス: インジェストクライアント例外

フィールド:

|名前|Type|意味       
|-----------|----|------------------------------|
|サイズ| `long`| インジェクション ソースのサイズ
|MaxSize| `long`| インジェスに許される最大サイズ

追加情報:

インジェスト ソースが最大サイズ 4 GB を超えると、例外がスローされます。 サイズの検証は[、インジェストプロパティ クラス](kusto-ingest-client-reference.md#class-kustoingestionproperties)の IgnoreSizeLimit フラグでオーバーライドできますが[、1 GB より大きい単一のソースを取り込む](about-kusto-ingest.md#ingestion-best-practices)のはお勧めできません。

### <a name="uploadfiletotempblobingestclientexception"></a>クライアント例外を処理します。
インジェスト クライアントが一時 BLOB へのインジェスト用に提供されたファイルのアップロードに失敗したときに発生します。

基本クラス: インジェストクライアント例外

### <a name="directingestclientexception"></a>クライアントの例外を指定します。
直接取り込み中に一般エラーが発生したときに発生します。

基本クラス: インジェストクライアント例外

### <a name="queuedingestclientexception"></a>キューイング・エスト・クライアント例外
キューに入れ取り込み中にエラーが発生したときに発生します。

基本クラス: インジェストクライアント例外

### <a name="ingestclientaggregateexception"></a>を使用します。
取り込み中に 1 つ以上のエラーが発生したときに発生します。

基本クラス:[集計例外](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

フィールド:

|名前|Type|意味       
|-----------|----|------------------------------|
|インジェスティションエラー| `IList<IngestClientException>`| 取り込み中に発生したエラーとそれらに関連するソース
|グローバル エラー| `bool`| すべてのソースに対して例外が発生したかどうかを示します。

## <a name="errors-in-native-code"></a>ネイティブ コードのエラー
Kusto エンジンはネイティブ コードで記述されています。 ネイティブ コードのエラーの詳細については、[ネイティブ コードのエラーを](../../concepts/errorsinnativecode.md)参照してください。