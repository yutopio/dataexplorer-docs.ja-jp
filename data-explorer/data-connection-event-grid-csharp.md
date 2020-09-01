---
title: C# を使用して Azure Data Explorer 用に Event Grid データ接続を作成する
description: この記事では、C# を使用して Azure Data Explorer 用に Event Grid データ接続を作成する方法について学習します。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: 5ba4f61d051a89d0fd3851f3e5be4f344ea79e0b
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874139"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>C# を使用して Azure Data Explorer 用に Event Grid データ接続を作成する

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager テンプレート](data-connection-event-grid-resource-manager.md)


[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
 この記事では、C# を使用して Azure Data Explorer 用に Event Grid データ接続を作成します。

## <a name="prerequisites"></a>前提条件

* Visual Studio 2019 をインストールしていない場合は、**無料**の [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。
* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [クラスターとデータベース](create-cluster-database-csharp.md)を作成します
* [テーブルと列のマッピング](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)を作成します
* [データベースとテーブルのポリシー](database-table-policies-csharp.md) (オプション) を設定します
* [Event Grid サブスクリプションでストレージ アカウント](ingest-data-event-grid.md)を作成します

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Event Grid データ接続を追加する

次の例は、Event Grid データ接続をプログラムを使用して追加する方法を示しています。 Azure portal を使用して Event Grid データ接続を追加する方法については、「[Azure Data Explorer でイベント グリッド データ接続を作成する](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)」を参照してください。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**設定** | **推奨値** | **フィールドの説明**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | テナント ID。 ディレクトリ ID とも呼ばれます。|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | リソースの作成に使用するサブスクリプション ID。|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント ID。|
| clientSecret | *xxxxxxxxxxxxxx* | ご利用のテナント内のリソースにアクセスできるアプリケーションのクライアント シークレット。 |
| resourceGroupName | *testrg* | ご利用のクラスターを含むリソース グループの名前。|
| clusterName | *mykustocluster* | ご利用のクラスターの名前。|
| databaseName | *mykustodatabase* | ご利用のクラスター内のターゲット データベースの名前。|
| dataConnectionName | *myeventhubconnect* | データ接続の任意の名前。|
| tableName | *StormEvents* | ターゲット データベース内のターゲット テーブルの名前。|
| mappingRuleName | *StormEvents_CSV_Mapping* | ターゲット テーブルに関連付けられている列マッピングの名前。|
| dataFormat | *csv* | メッセージのデータ形式。|
| eventHubResourceId | *リソース ID* | Event Grid がイベントを送信するように構成されているイベント ハブのリソース ID。 |
| storageAccountResourceId | *リソース ID* | インジェスト用のデータを保持しているストレージ アカウントのリソース ID。 |
| consumerGroup | *$Default* | ご利用のイベント ハブのコンシューマー グループ。|
| location | *米国中部* | データ接続リソースの場所。|

## <a name="generate-sample-data"></a>サンプル データを作成する

Azure Data Explorer とストレージ アカウントが接続されたので、サンプル データを作成してストレージにアップロードできます。

> [!NOTE]
> Azure Data Explorer では、BLOB 投稿の取り込みは削除されません。 BLOB の削除を管理する [Azure Blob Storage のライフサイクル](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)を使用して、BLOB を 3 から 5 日間保持します。

### <a name="upload-file-using-azure-blob-storage-sdk"></a>Azure Blob Storage SDK を使用してファイルをアップロードする

次のコード スニペットは、ストレージ アカウントに新しいコンテナーを作成し、そのコンテナーに既存ファイルを (BLOB として) アップロードしてから、コンテナー内の BLOB を一覧表示します。

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName = <container_name>;
var blobName = <blob_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mappingReference>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
blob.Metadata.Add("kustoIngestionMappingReference", mapping);
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

### <a name="upload-file-using-azure-data-lake-sdk"></a>Azure Data Lake SDK を使用してファイルをアップロードする

Data Lake Storage Gen2 を使用する場合は、[Azure Data Lake SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) を使用してファイルをストレージにアップロードできます。 次のコード スニペットは、Azure Data Lake Storage に新しいファイル システムを作成し、そのファイル システムにメタデータを含むローカル ファイルをアップロードします。

```csharp
var accountName = <storage_account_name>;
var accountKey = <storage_account_key>;
var fileSystemName = <file_system_name>;
var fileName = <file_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mapping_reference>;

var sharedKeyCredential = new StorageSharedKeyCredential(accountName, accountKey);
var dfsUri = "https://" + accountName + ".dfs.core.windows.net";
var dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), sharedKeyCredential);

// Create the filesystem and an empty file
var dataLakeFileSystemClient = dataLakeServiceClient.CreateFileSystem(fileSystemName).Value;
var dataLakeFileClient = dataLakeFileSystemClient.CreateFile(fileName).Value;

// Set metadata
IDictionary<String, String> metadata = new Dictionary<string, string>();
metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
metadata.Add("kustoIngestionMappingReference", mapping);
dataLakeFileClient.SetMetadata(metadata);

// Write to the file and close it
var fileStream = File.OpenRead(localFileName);
var fileSize = fileStream.Length;
dataLakeFileClient.Append(fileStream, offset: 0);
dataLakeFileClient.Flush(position: fileSize, close: true); // Note: This line triggers the event being processed by the data connection
```

> [!NOTE]
> [Azure Data Lake SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) を使用してファイルをアップロードする場合、[CreateFile](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createfile?view=azure-dotnet) の最初の 呼び出しでは、サイズが 0 の Event Grid イベントがトリガーされますが、このイベントは Azure Data Explorer によって無視されます。 "close" パラメーターを "true" に設定して Flush を呼び出すと、別のイベントがトリガーされます。 このイベントは、これが最終更新であり、ファイル ストリームがクローズされたことを示します。 このイベントは、Event Grid データ接続によって処理されます。 フラッシュについて詳しくは、[Azure Data Lake の Flush メソッド](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flush?view=azure-dotnet)に関するページを参照してください。

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
