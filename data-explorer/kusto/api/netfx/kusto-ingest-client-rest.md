---
title: Kusto を使用しないデータインジェストの使用。インジェストライブラリ-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Kusto を使用せずにデータを取り込む方法について説明します。 Azure データエクスプローラーでライブラリを取り込みます。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: 2ea7fd33a6e6ed8728fb12d53fbe76eadf8fd6b6
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862073"
---
# <a name="howto-data-ingestion-without-kustoingest-library"></a>Kusto を使用せずにデータを取り込む方法。インジェストライブラリ

## <a name="when-to-consider-not-using-kustoingest-library"></a>Kusto. インジェスト library を使用しないことを検討する場合
一般に、Kusto を使用する場合は、Kusto 取り込みを使用することをお勧めします。<BR>
これが (通常 OS の制約のために) オプションではない場合、いくつかの作業でほぼ同じ機能を実現できます。<BR>
この記事では、Kusto. インジェストパッケージに依存しないで、Kusto に**キューに格納**されたインジェストを実装する方法について説明します。

>**注:** 次のコードは C# で記述されており、サンプルコードを単純化するために、Azure Storage SDK、ADAL 認証ライブラリ、および NewtonSoft. JSON パッケージを使用しています。<BR>必要に応じて、対応するコードを適切な[Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)呼び出し、 [non-.NET ADAL パッケージ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)、使用可能な任意の JSON 処理パッケージに置き換えることができます。

## <a name="overview"></a>概要
次のコードサンプルでは、Kusto. インジェストライブラリを使用せずに kusto データ管理サービスを介して kusto にデータを取り込み、キューに登録されていることを示します。<BR>
これは、環境またはその他の制限のために完全な .NET がアクセスできない場合や利用できない場合に便利です。<BR>

> この記事では、運用レベルのパイプラインに推奨されるインジェストモードについて説明します。これは**キューインジェスト**とも呼ばれます (Kusto. インジェストライブラリの観点からは、対応するエンティティは[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient)インターフェイスです)。 このモードでは、クライアントコードは、Azure キューにインジェスト通知メッセージを投稿することで Kusto サービスとやり取りします。これは、Kusto データ管理 ( インジェスト) サービス。 データ管理サービスとの対話は、 **AAD**で認証される必要があります。

このフローの概要については、以下のコードサンプルで説明しています。次のように構成されています。
1. Azure Storage クライアントを作成し、データを blob にアップロードする
1. Kusto インジェストサービスにアクセスするための認証トークンを取得する
2. Kusto インジェストサービスに対してクエリを実行し、次のことを取得します。
    * インジェストリソース (キューと blob コンテナー)
    * すべてのインジェストメッセージに追加される kusto id トークン
3. Kusto in から取得したいずれかの blob コンテナーの blob にデータをアップロードする (2)
4. 対象の DB とテーブルを識別し、から blob を指すインジェストメッセージを作成します (3)。
5. (4) で構成したインジェストメッセージを、Kusto in から取得したインジェストキューの1つ (2) に投稿します。
6. インジェスト中にサービスによって発生したエラーを取得します

以降のセクションでは、各手順について詳しく説明します。

```csharp
// A container class for ingestion resources we are going to obtain from Kusto
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Kusto ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Kusto.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Kusto.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the queues in order to prevent throttling
    PostMessageToQueue(ingestionResources.IngestionQueues.First(), ingestionMessage);

    Thread.Sleep(20000);

    // 6a. Read success notifications
    var successes = PopTopMessagesFromQueue(ingestionResources.SuccessNotificationsQueue, 32);
    foreach (var sm in successes)
    {
        Console.WriteLine($"Ingestion completed: {sm}");
    }

    // 6b. Read failure notifications
    var errors = PopTopMessagesFromQueue(ingestionResources.FailureNotificationsQueue, 32);
    foreach (var em in errors)
    {
        Console.WriteLine($"Ingestion error: {em}");
    }
}
```

## <a name="1-obtain-authentication-evidence-from-aad"></a>1. AAD から認証証拠を取得する
ここでは、ADAL を使用して、入力キューを要求するために Kusto データ管理サービスにアクセスするための AAD トークンを取得します。
ADAL は、必要に応じて[、Windows 以外のプラットフォーム](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)で使用できます。
```csharp
// Authenticates the interactive user and retrieves AAD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT AAD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD Tenant ID or name}");

    // Acquire user token for the interactive user for Kusto:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

## <a name="2-retrieve-kusto-ingestion-resources"></a>2. Kusto インジェストリソースを取得する
これは興味深いものです。 ここでは、Kusto データ管理サービスに対する HTTP POST 要求を手動で作成し、インジェストリソースを返すように要求します。
これには、DM サービスがリッスンしているキュー、およびデータをアップロードするための blob コンテナーが含まれます。
データ管理サービスは、これらのキューのいずれかに到着したインジェスト要求を含むすべてのメッセージを処理します。
```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Kusto Ingestion service using supplied Access token
internal static IngestionResourcesSnapshot RetrieveIngestionResources(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get ingestion resources\" }}";

    IngestionResourcesSnapshot ingestionResources = new IngestionResourcesSnapshot();

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        IEnumerable<JToken> tokens;

        // Input queues
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SecuredReadyForAggregationQueue')]");
        foreach (var token in tokens)
        {
            ingestionResources.IngestionQueues.Add((string) token[1]);
        }

        // Temp storage containers
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'TempStorage')]");
        foreach (var token in tokens)
        {
            ingestionResources.TempStorageContainers.Add((string)token[1]);
        }

        // Failure notifications queue
        var singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'FailedIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.FailureNotificationsQueue = (string)singleToken;

        // Success notifications queue
        singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SuccessfulIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.SuccessNotificationsQueue = (string)singleToken;
    }

    return ingestionResources;
}

// Executes a POST request on provided URI using supplied Access token and request body
internal static WebResponse SendPostRequest(string uriString, string authToken, string body)
{
    WebRequest request = WebRequest.Create(uriString);

    request.Method = "POST";
    request.ContentType = "application/json";
    request.ContentLength = body.Length;
    request.Headers.Set(HttpRequestHeader.Authorization, $"Bearer {authToken}");

    Stream bodyStream = request.GetRequestStream();
    using (StreamWriter sw = new StreamWriter(bodyStream))
    {
        sw.Write(body);
        sw.Flush();
    }

    bodyStream.Close();
    return request.GetResponse();
}
```

## <a name="obtaining-kusto-identity-token"></a>Kusto Id トークンを取得しています
データインジェストを承認するための重要な手順は、id トークンを取得し、すべての取り込みメッセージにアタッチすることです。 インジェストメッセージは、非ダイレクトチャネル (Azure キュー) を介して Kusto に渡されるため、帯域内承認検証を実行することはできません。 Id トークンメカニズムでは、Kusto で署名された id の証拠を発行して、Kusto サービスがインジェストメッセージを受信した後に検証できるようにします。
```csharp
// Retrieves a Kusto identity token that will be added to every ingest message
internal static string RetrieveKustoIdentityToken(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get kusto identity token\" }}";
    string jsonPath = "Tables[0].Rows[*].[0]";

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        JToken identityToken = responseJson.SelectTokens(jsonPath).FirstOrDefault();

        return ((string)identityToken);
    }
}
```

## <a name="3-upload-data-to-azure-blob-container"></a>3. Azure Blob コンテナーにデータをアップロードする
この手順では、ローカルファイルを Azure Blob にアップロードします。この Blob は、後でインジェスト用に渡されます。 このコードは Azure Storage SDK を利用していますが、この依存関係が不可能な場合は、 [Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api)と同じように実現できます。
```csharp
// Uploads a single local file to an Azure Blob container, returns blob URI and original data size
internal static string UploadFileToBlobContainer(string filePath, string blobContainerUri, string containerName, string blobName, out long blobSize)
{
    var blobUri = new Uri(blobContainerUri);
    CloudBlobContainer blobContainer = new CloudBlobContainer(blobUri);
    CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference(blobName);

    using (Stream stream = File.OpenRead(filePath))
    {
        blockBlob.UploadFromStream(stream);
        blobSize = blockBlob.Properties.Length;
    }

    return string.Format("{0}{1}", blockBlob.Uri.AbsoluteUri, blobUri.Query);
}
```

## <a name="4-compose-kusto-ingestion-message"></a>4. Kusto インジェストメッセージを作成する
ここでは、NewtonSoft. JSON パッケージをもう一度使用して、適切な Kusto データ管理サービスがリッスンしている Azure キューに送信される有効なインジェスト要求メッセージを作成します。
* これはインジェストメッセージの最小要件です。
* この id トークンは必須であり、 `AdditionalProperties` JSON オブジェクト内に存在する必要があることに注意してください。
* 必要に応じ`CsvMapping`て、 `JsonMapping`またはプロパティも指定する必要があります
* 詳細については[、インジェストマッピングの事前作成に関する記事を](../../management/create-ingestion-mapping-command.md)参照してください。
* [付録 a](#appendix-a-ingestion-message-internal-structure)インジェストメッセージの構造について説明します。

```csharp
internal static string PrepareIngestionMessage(string db, string table, string dataUri, long blobSizeBytes, string mappingRef, string identityToken)
{
    var message = new JObject();

    message.Add("Id", Guid.NewGuid().ToString());
    message.Add("BlobPath", dataUri);
    message.Add("RawDataSize", blobSizeBytes);
    message.Add("DatabaseName", db);
    message.Add("TableName", table);
    message.Add("RetainBlobOnSuccess", true);   // Do not delete the blob on success
    message.Add("Format", "json");              // Data is in JSON format
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef)));
    return message.ToString();
}
```

## <a name="5-post-kusto-ingestion-message-to-kusto-ingestion-queue"></a>5. Kusto インジェストメッセージを Kusto インジェストキューに投稿する
最後に、deed 自体は、選択したキューに構築されたメッセージを投稿するだけです。<BR>
注: .Net ストレージクライアントを使用する場合は、既定でメッセージが base64 にエンコードされます。 Storage の[ドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage)を参照してください。<BR>
このクライアントを使用していない場合は、メッセージの内容を適切にエンコードしてください。

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

## <a name="6-pop-messages-from-an-azure-queue"></a>6. Azure キューからの Pop メッセージ
インジェスト後、このメソッドを使用して、Kusto データ管理サービスによって書き込まれる適切なキューからエラーメッセージを読み取ります。<BR>
[「付録 B](#appendix-b-ingestion-failure-message-structure) 」では、エラーメッセージの構造について説明します。

```csharp
internal static IEnumerable<string> PopTopMessagesFromQueue(string queueUriWithSas, int count)
{
    List<string> messages = Enumerable.Empty<string>().ToList();
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    var messagesFromQueue = queue.GetMessages(count);
    foreach (var m in messagesFromQueue)
    {
        messages.Add(m.AsString);
        queue.DeleteMessage(m);
    }

    return messages;
}
```

## <a name="appendix-a-ingestion-message-internal-structure"></a>付録 A: インジェストメッセージの内部構造
Kusto データ管理サービスが入力 Azure キューからの読み取りを想定しているメッセージは、次の形式の JSON ドキュメントです。

```json
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "Format" : "<csv|tsv|...>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```


|プロパティ | 説明 |
|---------|-------------|
|Id |メッセージ識別子 (GUID) |
|BlobPath |Blob URI。読み取り/書き込み/削除のためのアクセス許可を Kusto に付与する SAS キーを含みます (書き込み/削除アクセス許可は、Kusto がデータの取り込み完了後に blob を削除する場合に必要です)。 |
|RawDataSize |圧縮されていないデータのサイズ (バイト単位)。 この値を指定すると、Kusto は複数の blob をまとめて集計できる可能性があるため、インジェストを最適化できます。 このプロパティは省略可能ですが、指定されていない場合、Kusto は blob にアクセスしてサイズを取得します。 |
|DatabaseName |ターゲットデータベース名 |
|TableName |ターゲットテーブル名 |
|RetainBlobOnSuccess |に設定する`true`と、インジェストが正常に完了した後、blob は削除されません。 既定値は `false` です |
|Format |非圧縮データ形式 |
|FlushImmediately ちに |に設定する`true`と、すべての集計がスキップされます。 既定値は `false` です |
|ReportLevel |成功/エラー報告レベル: 0-失敗、1-なし、2-すべて |
|ReportMethod |レポートメカニズム: 0-キュー、1-テーブル |
|AdditionalProperties |その他のプロパティ (タグなど) |


## <a name="appendix-b-ingestion-failure-message-structure"></a>付録 B: インジェストのエラーメッセージの構造
Kusto データ管理サービスが入力 Azure キューからの読み取りを想定している次の表のメッセージは、JSON ドキュメントであり、次の形式になっています。

|プロパティ | 説明 |
|---------|-------------
|OperationId |サービス側での操作を追跡するために使用できる操作識別子 (GUID) |
|データベース |ターゲットデータベース名 |
|テーブル |ターゲットテーブル名 |
|失敗した場合 |失敗のタイムスタンプ |
|IngestionSourceId |Kusto の取り込みに失敗したデータチャンクを識別する GUID |
|IngestionSourcePath |Kusto の取り込みに失敗したデータチャンクへのパス (URI) |
|詳細 |エラー メッセージ |
|ErrorCode |Kusto エラーコード ([こちら](kusto-ingest-client-errors.md#ingestion-error-codes)のすべてのエラーコードを参照してください) |
|FailureStatus |障害が永続的であるか一時的なものであるかを示します |
|RootActivityId |サービス側での操作を追跡するために使用できる kusto 関連付け識別子 (GUID) |
|発信ポリシー |エラーの原因がエラー[トランザクション更新ポリシー](../../management/updatepolicy.md)であるかどうかを示します |
|再試行する | が正常に再試行された場合にインジェストが成功するかどうかを示します |