---
title: Event Grid サブスクリプションを使用したストレージからの取り込み-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Event Grid サブスクリプションを使用してストレージからインジェストを取り込む方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 88a95ea2fc8e1f417114cfcfd89c4e5003d9bef2
ms.sourcegitcommit: fb54d71660391a63b0c107a9703adea09bfc7cb9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2020
ms.locfileid: "86946106"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Event Grid サブスクリプションを使用したストレージからの取り込み

Azure データエクスプローラーは、blob が作成された通知の[Azure Event Grid](/azure/event-grid/overview)サブスクリプションを使用して Azure Storage (blob Storage と ADLSv2) から継続的にインジェストを提供し、これらの通知を Event Hub 経由で Azure データエクスプローラーにストリーミングします。

## <a name="data-format"></a>データ形式

* Blob は、[サポートされている](../../../ingestion-supported-formats.md)任意の形式にすることができます。
* Blob は圧縮できます。 詳細については、「[サポートされている圧縮](../../../ingestion-supported-formats.md#supported-data-compression-formats)」を参照してください。

> [!NOTE]
> 理想的には、元の非圧縮データサイズを blob メタデータの一部にする必要があります。
> 圧縮されていないサイズが指定されていない場合は、ファイルサイズに基づいて Azure データエクスプローラーによって推定されます。 Blob メタデータのプロパティを圧縮されて `rawSizeBytes` [property](#ingestion-properties)いないデータサイズ (バイト単位) に設定することによって、元のデータサイズを指定できます。
> 
> ファイルあたりのインジェストの非圧縮サイズ制限は 4 GB です。

## <a name="ingestion-properties"></a>インジェストのプロパティ

Blob メタデータを使用して、blob インジェストの[インジェストプロパティ](../../../ingestion-properties.md)を指定できます。
以下のプロパティを設定できます。

|プロパティ | 説明|
|---|---|
| rawSizeBytes | 未加工の (圧縮されていない) データのサイズ。 Avro/ORC/Parquet の場合、この値は、フォーマット固有の圧縮が適用される前のサイズです。|
| kustoTable |  既存のターゲット テーブルの名前。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| kustoDataFormat |  データ形式。 **データ接続**ブレードで設定された**データ形式**を上書きします。 |
| kustoIngestionMappingReference |  使用する既存のインジェスト マッピングの名前。 **データ接続**ブレードで設定されている**列マッピング**を上書きします。|
| kustoIgnoreFirstRecord | に設定されている場合 `true` 、Azure データエクスプローラーは blob の最初の行を無視します。 表形式のデータ (CSV、TSV など) でヘッダーを無視するために使用します。 |
| kustoExtentTags | 結果のエクステントに添付される[タグ](../extents-overview.md#extent-tagging)を表す文字列。 |
| kustoCreationTime |  ISO 8601 の文字列として書式設定されている、BLOB の [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) をオーバーライドします。 バックフィルに使用します。 |

## <a name="events-routing"></a>イベントのルーティング

Azure データエクスプローラークラスターへの blob ストレージ接続を設定する場合は、ターゲットテーブルのプロパティを指定します。
* テーブル名
* データ形式
* mapping

このセットアップは、データの既定のルーティングです (と呼ばれることもあり `static routing` ます)。
Blob メタデータを使用して、各 blob のターゲットテーブルのプロパティを指定することもできます。 データは、[インジェストプロパティ](#ingestion-properties)によって指定されたとおりに動的にルーティングされます。

次に示すのは、アップロードする前に、インジェストプロパティを blob メタデータに設定する例です。 Blob は異なるテーブルにルーティングされます。

データの生成方法の詳細については、「[サンプルコード](#generating-data)」を参照してください。

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>お使いのストレージ アカウント内に Event Grid サブスクリプションを作成する

> [!NOTE]
> 最適なパフォーマンスを得るには、Azure データエクスプローラークラスターと同じリージョンにすべてのリソースを作成します。

### <a name="prerequisites"></a>前提条件

* [ストレージ アカウントの作成](/azure/storage/common/storage-quickstart-create-account)。
  Event Grid 通知サブスクリプションは、種類がまたはの Azure Storage アカウントで設定でき `BlobStorage` `StorageV2` ます。
  [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction)の有効化もサポートされています。
* [イベントハブを作成](/azure/event-hubs/event-hubs-create)します。

### <a name="event-grid-subscription"></a>Event Grid サブスクリプション
 
1. Azure portal でストレージ アカウントを検索します。
1. 左側のメニューで、[**イベント**  >  **イベントサブスクリプション**] を選択します。

     :::image type="content" source="../images/eventgrid/create-event-grid-subscription-1.png" alt-text="Event grid サブスクリプションの作成":::

1. **[基本]** タブの **[イベント サブスクリプションの作成]** ウィンドウで、次の値を指定します。

    :::image type="content" source="../images/eventgrid/create-event-grid-subscription-2.png" alt-text="入力するイベントサブスクリプションの値を作成する":::

    |**設定** | **推奨値** | **フィールドの説明**|
    |---|---|---|
    | 名前 | *test-grid-connection* | 作成する event grid サブスクリプションの名前。|
    | イベント スキーマ | *Event Grid スキーマ* | イベント グリッドで使用するスキーマ。 |
    | トピックの種類 | *ストレージ アカウント* | イベント グリッド トピックの種類。 |
    | ソースリソース | *gridteststorage1* | ご利用のストレージ アカウントの名前。 |
    | [システム トピック名] | *gridteststorage1...* | Azure Storage がイベントを発行するシステムトピック。 このシステムトピックでは、イベントを受信して処理するサブスクライバーにイベントを転送します。 |
    | イベントの種類のフィルター処理 | *作成された Blob* | 通知を取得する特定のイベント。 現在サポートされている型は、Microsoft. ストレージ. BlobCreated です。 サブスクリプションを作成するときは、必ずそれを選択してください。|
    | エンドポイントの種類 | *Event Hubs* | イベントの送信先であるエンドポイントの種類。 |
    | エンドポイント | *test-hub* | 作成したイベント ハブ。 |

1. 特定の件名を追跡する場合は、[**フィルター** ] タブを選択します。 次のように、通知用のフィルターを設定します。
   * **[サブジェクト フィルタリングを有効にする]** を選択します。
   * Subject は、subject の*リテラル*プレフィックスである Field**で始まり**ます。 適用されるパターンは*startswith*であるため、複数のコンテナー、フォルダー、または blob にまたがることができます。 ワイルドカードは使用できません。
       * BLOB コンテナーに対してフィルターを定義するには、フィールドを *`/blobServices/default/containers/[container prefix]`* のように設定する "*必要があります*"。
       * BLOB プレフィックス (または Azure Data Lake Gen2 のフォルダー) に対してフィルターを定義するには、フィールドを *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* のように設定する "*必要があります*"。
   * **[Subject Ends With]\(指定の値で終わる件名\)** フィールドは、BLOB の "*リテラル*" サフィックスです。 ワイルドカードは使用できません。
   * **大文字と小文字を区別する件名一致**フィールドは、プレフィックスとサフィックスのフィルターで大文字と小文字を区別するかどうかを示します。
   * イベントのフィルター処理の詳細については、[Blob Storage のイベント](/azure/storage/blobs/storage-blob-event-overview#filtering-events)に関するセクションを参照してください。
    
        :::image type="content" source="../images/eventgrid/filters-tab.png" alt-text="[フィルター] タブのイベントグリッド":::

> [!NOTE]
> エンドポイントがイベントの受信を認識しない場合、Azure Event Grid は再試行メカニズムをアクティブ化します。 この再試行の配信が失敗した場合、Event Grid 配信*不能*のプロセスを使用して、配信されていないイベントをストレージアカウントに配信します。 詳細については、[Event Grid のメッセージの配信と再試行](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration)に関する記事を参照してください。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure データエクスプローラーへのデータインジェスト接続

* Via Azure portal: [Azure データエクスプローラーで Event Grid データ接続を作成](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)します。
* Kusto management .NET SDK の使用: [Event Grid データ接続を追加する](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Kusto management Python SDK の使用: [Event Grid データ接続を追加する](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* [Event Grid データ接続を追加するための Azure Resource Manager テンプレート](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)を使用する

### <a name="generating-data"></a>データを生成する

> [!NOTE]
> * `BlockBlob`データを生成するには、を使用します。 `AppendBlob` はサポートされません。

ローカルファイルから blob を作成し、インジェストプロパティを blob メタデータに設定して、アップロードする例を次に示します。

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob's metadata & uploading the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> Azure Data Lake Gen2 storage SDK を使用するには、を使用して `CreateFile` ファイルをアップロードし、 `Flush` 最後に close パラメーターを "true" に設定する必要があります。
> Data Lake Gen2 SDK を正しく使用する方法の詳細な例については、「 [AZURE DATA LAKE SDK を使用](../../../data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk)したファイルのアップロード」を参照してください。

## <a name="blob-lifecycle"></a>Blob のライフサイクル

Azure データエクスプローラーは、インジェスト後に blob を削除しません。 [Azure blob storage のライフサイクル](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)を使用して、blob の削除を管理します。 Blob は 3 ~ 5 日間保持することをお勧めします。

## <a name="known-issues"></a>既知の問題

* Azure データエクスプローラーを使用して event grid のインジェストに使用するファイルを[エクスポート](../data-export/export-data-to-storage.md)する場合は、次の点に注意してください。 
    * エクスポートコマンドに指定された接続文字列、または[外部テーブル](../data-export/export-data-to-an-external-table.md)に対して指定された接続文字列が[ADLS Gen2 形式](../../api/connection-strings/storage.md#azure-data-lake-store)の接続文字列 (など) で `abfss://filesystem@accountname.dfs.core.windows.net` あっても、ストレージアカウントが階層的な名前空間に対して有効になっていない場合、Event Grid 通知はトリガーされません。 
    * アカウントが階層的な名前空間に対して有効になっていない場合、接続文字列には[Blob Storage](../../api/connection-strings/storage.md#azure-storage-blob)形式 (など) を使用する必要があり `https://accountname.blob.core.windows.net` ます。 ADLS Gen2 接続文字列を使用している場合でもエクスポートは想定どおりに動作しますが、通知はトリガーされず Event Grid インジェストは機能しません。
