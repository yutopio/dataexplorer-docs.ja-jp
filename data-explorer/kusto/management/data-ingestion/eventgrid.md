---
title: Event Grid サブスクリプションを使用したストレージからの取り込み-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Event Grid サブスクリプションを使用してストレージからインジェストを取り込む方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 7673b50a8d1ff401f8c2fa086b7eec34c0517238
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373482"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Event Grid サブスクリプションを使用したストレージからの取り込み

Azure データエクスプローラーは、blob が作成された通知の[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview)サブスクリプションを使用して Azure Storage (blob Storage と ADLSv2) から継続的にインジェストを提供し、これらの通知を Event Hub 経由で kusto にストリーミングします。

## <a name="data-format"></a>データ形式

* Blob は、[サポートされている](../../../ingestion-supported-formats.md)任意の形式にすることができます。
* Blob は、[サポートされている](../../../ingestion-supported-formats.md#supported-data-compression-formats)任意の圧縮で圧縮できます。

> [!NOTE]
> 理想的には、元の非圧縮データサイズを blob メタデータの一部にする必要があります。
> 圧縮されていないサイズが指定されていない場合、Azure データエクスプローラーによってファイルサイズに基づいて推定されます。 `rawSizeBytes`Blob メタデータの[プロパティ](#ingestion-properties)を**圧縮**されていないデータサイズ (バイト単位) に設定して、元のデータサイズを指定します。
> 4 GB のファイルごとにインジェストの非圧縮サイズ制限があることに注意してください。

## <a name="ingestion-properties"></a>インジェストのプロパティ

Blob メタデータを使用して、blob インジェストの[インジェストプロパティ](../../../ingestion-properties.md)を指定できます。
以下のプロパティを設定できます。

|プロパティ | 説明|
|---|---|
| rawSizeBytes | 未加工の (圧縮されていない) データのサイズ。 Avro/ORC/Parquet の場合、これは形式固有の圧縮が適用される前のサイズです。|
| kustoTable |  既存のターゲット テーブルの名前。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| kustoDataFormat |  データ形式。 [`Data Connection`] ブレードでの [`Data format`] の設定をオーバーライドします。 |
| kustoIngestionMappingReference |  使用する既存のインジェスト マッピングの名前。 [`Data Connection`] ブレードでの [`Column mapping`] の設定をオーバーライドします。|
| kustoIgnoreFirstRecord | に設定されている場合 `true` 、Azure データエクスプローラーは blob の最初の行を無視します。 表形式のデータ (CSV、TSV など) でヘッダーを無視するために使用します。 |
| kustoExtentTags | 結果のエクステントに添付される[タグ](../extents-overview.md#extent-tagging)を表す文字列。 |
| kustoCreationTime |  ISO 8601 の文字列として書式設定されている、BLOB の [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) をオーバーライドします。 バックフィルに使用します。 |

## <a name="events-routing"></a>イベントのルーティング

Azure データエクスプローラークラスターへの blob ストレージ接続を設定する場合は、ターゲットテーブルのプロパティ (テーブル名、データ形式、およびマッピング) を指定します。 これは、データの既定のルーティングであり、とも呼ばれ `static routig` ます。
Blob メタデータを使用して、各 blob のターゲットテーブルのプロパティを指定することもできます。 データは、[インジェストプロパティ](#ingestion-properties)によって指定されたとおりに動的にルーティングされます。

次に示すのは、アップロードする前に、インジェストプロパティを blob メタデータに設定する例です。 Blob は異なるテーブルにルーティングされます。

データを生成する方法の詳細については、[サンプルコード](#generating-data)を参照してください。

 ```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-event-grid-subscription"></a>Event Grid サブスクリプションを作成する

> [!Note]
> 最適なパフォーマンスを得るには、Azure データエクスプローラークラスターと同じリージョンにすべてのリソースを作成します。

### <a name="prerequisites"></a>前提条件

* [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
  Event Grid 通知サブスクリプションは、種類がまたはの Azure Storage アカウントで設定でき `BlobStorage` `StorageV2` ます。 
  [Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)の有効化もサポートされています。
* [イベントハブを作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)します。

### <a name="event-grid-subscription"></a>Event Grid サブスクリプション

* `Event Hub`Blob storage イベント通知の転送に使用される、以下型として選択された kusto。 `Event Grid schema`通知用に選択されたスキーマです。 各ハブが1つの接続を提供できることに注意してください。
* Blob storage サブスクリプション接続は、型の通知を処理し `Microsoft.Storage.BlobCreated` ます。 サブスクリプションを作成するときは、必ずそれを選択してください。 他の種類の通知 (選択されている場合) は無視されることに注意してください。
* 1つのサブスクリプションでストレージイベントを1つ以上のコンテナーに通知できます。 特定のコンテナーのファイルを追跡する場合は、通知のフィルターを次のように設定します。接続を設定するときに、次の値を speciel に扱います。 
   * サブジェクトは、blob コンテナーの*リテラル*プレフィックスである Filter**で始まり**ます。 適用されるパターンは *startswith* であるため、複数のコンテナーにまたがることができます。 ワイルドカードは使用できません。
     次のように設定する*必要があり* *`/blobServices/default/containers/<prefix>`* ます。 例: */blobServices/default/containers/StormEvents-2020-*
   * **[Subject Ends With]\(指定の値で終わる件名\)** フィールドは、BLOB の "*リテラル*" サフィックスです。 ワイルドカードは使用できません。 ファイル拡張子をフィルター処理する場合に便利です。

詳細なチュートリアルについては、「[ストレージアカウントガイド」の「Event Grid サブスクリプションを作成](../../../ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)する方法」を参照してください。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure データエクスプローラーへのデータインジェスト接続

* Azure Portal を使用する: [azure データエクスプローラーで Event Grid データ接続を作成](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)します。
* Kusto management .NET SDK の使用: [Event Grid データ接続を追加する](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Kusto management Python SDK の使用: [Event Grid データ接続を追加する](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* ARM テンプレートを使用した場合: [Event Grid データ接続を追加するための Azure Resource Manager テンプレート](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>データの生成

> [!NOTE]
> * `BlockBlob`データを生成するには、を使用します。 `AppendBlob` はサポートされません。
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

ローカルファイルから blob を作成し、blob メタデータにインジェストプロパティを設定してアップロードする例を次に示します。

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

## <a name="blob-lifecycle"></a>Blob のライフサイクル

Azure データエクスプローラーでは、取り込み後の blob は削除されませんが、3 ~ 5 日間保持されます。 [Azure blob storage のライフサイクル](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)を使用して、blob の削除を管理します。