---
title: インジェストライブラリを使用しない kusto データインジェスト-Azure データエクスプローラー
description: この記事では、Kusto を使用せずにデータを取り込む方法について説明します。 Azure データエクスプローラーでライブラリを取り込みます。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: 10f59a167de12e4b688f6d9b5f15d3f0f15d8291
ms.sourcegitcommit: f689547c0f77b1b8bfa50a19a4518cbbc6d408e5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89557396"
---
# <a name="ingestion-without-kustoingest-library"></a>Kusto によるインジェストを使用した取り込み

Azure データエクスプローラーにデータを取り込みする場合は、Kusto. インジェストライブラリをお勧めします。 ただし、Kusto. インジェストパッケージに依存しなくても、ほぼ同じ機能を実現できます。
この記事では、Azure データエクスプローラーへの *キューインジェスト* を使用して、運用レベルのパイプラインを作成する方法について説明します。

> [!NOTE]
> 次のコードは C# で記述されており、Azure Storage SDK、ADAL 認証ライブラリ、およびパッケージの NewtonSoft.JSを利用して、サンプルコードを簡略化しています。 必要に応じて、対応するコードを適切な [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) 呼び出し、 [non-.NET ADAL パッケージ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)、および使用可能な任意の JSON 処理パッケージに置き換えることができます。

この記事では、推奨されるインジェストモードについて説明します。 Kusto. インジェストライブラリの場合、対応するエンティティは [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) インターフェイスです。 ここでは、クライアントコードが azure キューにインジェスト通知メッセージを投稿することによって、Azure データエクスプローラーサービスと対話します。 メッセージへの参照は、Kusto データ管理 (インジェスト) サービスから取得されます。 サービスとの対話は、Azure Active Directory (Azure AD) を使用して認証される必要があります。

次のコードは、kusto データ管理サービスが、Kusto. インジェストライブラリを使用せずにキューに置かれたデータインジェストを処理する方法を示しています。 この例は、環境またはその他の制限のために完全な .NET がアクセスできないか、使用できない場合に役立ちます。

このコードには、Azure Storage クライアントを作成し、データを blob にアップロードする手順が含まれています。
各手順の詳細については、サンプルコードの後で説明します。

1. [Azure データエクスプローラーインジェストサービスにアクセスするための認証トークンを取得する](#obtain-authentication-evidence-from-azure-ad)
1. Azure データエクスプローラーインジェストサービスにクエリを実行し、次のものを取得します。
    * [インジェストリソース (キューと blob コンテナー)](#retrieve-azure-data-explorer-ingestion-resources)
    * [すべてのインジェストメッセージに追加される Kusto id トークン](#obtain-a-kusto-identity-token)
1. [Kusto in から取得したいずれかの blob コンテナーの blob にデータをアップロードする (2)](#upload-data-to-the-azure-blob-container)
1. [コピー先のデータベースとテーブルを識別するインジェストメッセージを作成します。このメッセージは、から blob を指します (3)。](#compose-the-azure-data-explorer-ingestion-message)
1. [(4) で構成したインジェストメッセージを、(2) の Azure データエクスプローラーから取得したインジェストキューに投稿します。](#post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue)**
1. [インジェスト中にサービスによって検出されたエラーを取得します](#check-for-error-messages-from-the-azure-queue)

```csharp
// A container class for ingestion resources we are going to obtain from Azure Data Explorer
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Azure Data Explorer ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Azure Data Explorer.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Azure Data Explorer.
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

## <a name="using-queued-ingestion-to-azure-data-explorer-for-production-grade-pipelines"></a>Azure データエクスプローラーにキューインジェストを使用して実稼働レベルのパイプラインを作成する

### <a name="obtain-authentication-evidence-from-azure-ad"></a>Azure AD から認証証拠を取得する

ここでは、ADAL を使用して、Kusto データ管理サービスにアクセスし、その入力キューを要求する Azure AD トークンを取得します。
ADAL は、必要に応じて [、Windows 以外のプラットフォーム](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) で使用できます。

```csharp
// Authenticates the interactive user and retrieves Azure AD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT Azure AD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD Tenant ID or name}");

    // Acquire user token for the interactive user for Azure Data Explorer:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

### <a name="retrieve-azure-data-explorer-ingestion-resources"></a>Azure データエクスプローラーインジェストリソースを取得する

データ管理サービスに対して HTTP POST 要求を手動で作成し、インジェストリソースの返却を要求します。 これらのリソースには、DM サービスがリッスンしているキュー、およびデータをアップロードするための blob コンテナーが含まれます。
データ管理サービスは、これらのキューのいずれかに到着するインジェスト要求を含むすべてのメッセージを処理します。

```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Azure Data Explorer Ingestion service using supplied Access token
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

### <a name="obtain-a-kusto-identity-token"></a>Kusto id トークンを取得する

インジェストメッセージは、非ダイレクトチャネル (Azure キュー) を介して Azure データエクスプローラーに渡されます。これにより、Azure データエクスプローラーインジェストサービスにアクセスするための帯域内承認検証を行うことができなくなります。 この問題を解決するには、すべての取り込みメッセージに id トークンを添付します。 トークンは、帯域内承認検証を有効にします。 この署名付きトークンは、インジェストメッセージを受信したときに Azure データエクスプローラーサービスによって検証されます。

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

### <a name="upload-data-to-the-azure-blob-container"></a>Azure Blob コンテナーにデータをアップロードする

この手順では、取り込み用に渡される Azure Blob にローカルファイルをアップロードします。 このコードでは、Azure Storage SDK を使用します。 依存関係が不可能な場合は、 [Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api)で実現できます。

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

### <a name="compose-the-azure-data-explorer-ingestion-message"></a>Azure データエクスプローラーインジェストメッセージを作成する

パッケージの NewtonSoft.JSによって、ターゲットデータベースとテーブルを識別するための有効なインジェスト要求が再度作成され、その blob が参照されます。
メッセージは、関連する Kusto データ管理サービスがリッスンしている Azure キューに送信されます。

ここでは、考慮すべき点をいくつか紹介します。

* この要求は、インジェストメッセージの最小要件です。

> [!NOTE]
> Id トークンは必須であり、 **Additionalproperties** JSON オブジェクトの一部である必要があります。

* 必要に応じて、CsvMapping または JsonMapping プロパティも指定する必要があります
* 詳細については、 [インジェストマッピングの事前作成に関する記事](../../management/create-ingestion-mapping-command.md)を参照してください。
* セクション [インジェストメッセージの内部構造](#ingestion-message-internal-structure) インジェストメッセージの構造について説明します。

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
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef),
                                            // Data is in JSON format
                                            new JProperty("format", "json")));
    return message.ToString();
}
```

### <a name="post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue"></a>Azure データエクスプローラーインジェストメッセージを Azure データエクスプローラーインジェストキューに投稿する

最後に、作成したメッセージを、Azure データエクスプローラーから取得した選択したインジェストキューに送信します。

> [!NOTE]
> V12 未満の .net ストレージクライアントバージョン既定では、メッセージが base64 にエンコードされます。詳細については、 [Storage のドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage?view=azure-dotnet-legacy#Microsoft_WindowsAzure_Storage_Queue_CloudQueue_EncodeMessage)を参照してください。V12 を超える .Net ストレージクライアントバージョンを使用している場合は、メッセージの内容を適切にエンコードする必要があります。

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

### <a name="check-for-error-messages-from-the-azure-queue"></a>Azure キューからのエラーメッセージを確認する

インジェストの後、データ管理が書き込む関連キューからのエラーメッセージを確認します。 エラーメッセージの構造の詳細については、 [インジェストのエラーメッセージの構造](#ingestion-failure-message-structure)に関する説明を参照してください。 

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

## <a name="ingestion-messages---json-document-formats"></a>インジェストメッセージ-JSON ドキュメント形式

### <a name="ingestion-message-internal-structure"></a>インジェストメッセージの内部構造

Kusto データ管理サービスが入力 Azure キューからの読み取りを想定しているメッセージは、次の形式の JSON ドキュメントです。

```JSON
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```

|プロパティ | 説明 |
|---------|-------------|
|Id |メッセージ識別子 (GUID) |
|BlobPath |Blob へのパス (URI)。 Azure データエクスプローラーアクセス許可を付与する SAS キーを含み、読み取り/書き込み/削除を行います。 Azure データエクスプローラーがデータの取り込みを完了した後に blob を削除できるようにするためのアクセス許可が必要です。|
|RawDataSize |圧縮されていないデータのサイズ (バイト単位)。 この値を指定すると、Azure データエクスプローラーによって、複数の blob を集計する可能性があるため、インジェストを最適化できます。 このプロパティは省略可能ですが、指定されていない場合、Azure データエクスプローラーは blob にアクセスしてサイズを取得します。 |
|DatabaseName |ターゲットデータベース名 |
|TableName |ターゲットテーブル名 |
|RetainBlobOnSuccess |に設定すると `true` 、インジェストが正常に完了した後、blob は削除されません。 既定値は `false` です |
|FlushImmediately ちに |に設定する `true` と、すべての集計がスキップされます。 既定値は `false` です |
|ReportLevel |成功/エラー報告レベル: 0-失敗、1-なし、2-すべて |
|ReportMethod |レポートメカニズム: 0-キュー、1-テーブル |
|AdditionalProperties |、、など `format` の追加のプロパティ `tags` `creationTime` 。 詳細については、「 [データインジェストのプロパティ](../../../ingestion-properties.md)」を参照してください。|

### <a name="ingestion-failure-message-structure"></a>インジェストエラーメッセージの構造

データ管理が入力 Azure キューから読み取ることを想定しているメッセージは、次の形式の JSON ドキュメントです。

|プロパティ | 説明 |
|---------|-------------
|OperationId |サービス側での操作を追跡するために使用できる操作識別子 (GUID) |
|データベース |ターゲットデータベース名 |
|Table |ターゲットテーブル名 |
|失敗した場合 |失敗のタイムスタンプ |
|IngestionSourceId |Azure データエクスプローラーが取り込みに失敗したデータチャンクを識別する GUID |
|IngestionSourcePath |Azure データエクスプローラーが取り込みに失敗したデータチャンクへのパス (URI) |
|詳細 |エラー メッセージ |
|ErrorCode |Azure データエクスプローラーのエラーコード ( [こちら](kusto-ingest-client-errors.md#ingestion-error-codes)のすべてのエラーコードを参照してください) |
|FailureStatus |障害が永続的であるか一時的なものであるかを示します |
|RootActivityId |サービス側での操作を追跡するために使用できる Azure データエクスプローラー関連付け識別子 (GUID) |
|発信ポリシー |失敗した[トランザクション更新ポリシー](../../management/updatepolicy.md)が原因でエラーが発生したかどうかを示します。 |
|再試行する | がそのように再試行された場合にインジェストが成功するかどうかを示します |
