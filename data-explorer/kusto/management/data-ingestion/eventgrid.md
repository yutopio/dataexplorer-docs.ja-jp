---
title: イベント グリッド サブスクリプションを使用してストレージから取り込む - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで Event Grid サブスクリプションを使用してストレージから取り込む方法について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 682c39b11292c7e198dba46dad9b5bfa3b520c0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521524"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Event Grid サブスクリプションを使用したストレージからの取り込み

Azure データ エクスプローラーでは、Azure Storage (BLOB ストレージと ADLSv2) からの継続的な取り込みと[、Blob](https://docs.microsoft.com/azure/event-grid/overview)作成された通知用の Azure Event Grid サブスクリプションを提供し、イベント ハブを介してこれらの通知を Kusto にストリーミングします。

## <a name="data-format"></a>データ形式

* BLOB は、[サポートされている任意の形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)にすることができます。
* サポートされている圧縮のいずれかで BLOB を圧縮できます[。](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats#supported-data-compression-formats)

> [!NOTE]
> 理想的には、元の非圧縮データ サイズは、BLOB メタデータの一部である必要があります。
> 圧縮されていないサイズが指定されていない場合、Azure データ エクスプローラーはファイル サイズに基づいてサイズを推定します。 BLOB メタデータの`rawSizeBytes`[プロパティ](#ingestion-properties)を**非圧縮**データ サイズ (バイト単位) に設定して、元のデータ サイズを指定します。
> ファイルごとの圧縮されていないサイズ制限は 4 GB です。

## <a name="ingestion-properties"></a>インジェストのプロパティ

BLOB メタデータを使用して、BLOB インジェスティションのインジェクション[プロパティ](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)を指定できます。
以下のプロパティを設定できます。

|プロパティ | 説明|
|---|---|
| 生のサイズバイト | 未圧縮のデータのサイズ。 Avro/ORC/Parquet の場合、これはフォーマット固有の圧縮が適用される前のサイズです。|
| クストテーブル |  既存のターゲット表の名前。 ブレードの`Table`セットを上書`Data Connection`きします。 |
| クストデータフォーマット |  データ形式。 ブレードの`Data format`セットを上書`Data Connection`きします。 |
| クストインジングエプションマッピングリファレンス |  使用する既存の取り込みマッピングの名前。 ブレードの`Column mapping`セットを上書`Data Connection`きします。|
| ファーストレコードを無視する | に`true`設定すると、Azure データ エクスプローラーは BLOB の最初の行を無視します。 ヘッダーを無視するには、表形式データ (CSV、TSV、または類似) で使用します。 |
| クストエクステントタグ | 結果の範囲にアタッチされる[タグ](../extents-overview.md#extent-tagging)を表す文字列。 |
| クストクリエーションタイム |  ISO 8601 文字列として書式設定された blob の[$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer)をオーバーライドします。 バックフィルに使用します。 |

## <a name="events-routing"></a>イベントルーティング

Azure Data Explorer クラスターへの BLOB ストレージ接続を設定する場合は、ターゲット テーブルのプロパティ (テーブル名、データ形式、およびマッピング) を指定します。 これは、データの既定のルーティングです`static routig`。
BLOB メタデータを使用して、各 BLOB のターゲット テーブル プロパティを指定することもできます。 データは、[インジェクション プロパティ](#ingestion-properties)で指定されたとおりに動的にルーティングされます。

アップロードする前に、BLOB メタデータにインジェスト プロパティを設定する例を次に示します。 BLOB は、異なるテーブルにルーティングされます。

データの生成方法の詳細については、[サンプル コード](#generating-data)を参照してください。

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
> 最適なパフォーマンスを得るため、Azure データ エクスプローラー クラスターと同じリージョン内にすべてのリソースを作成します。

### <a name="prerequisites"></a>前提条件

* [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
  イベント グリッド通知サブスクリプションは、種類`BlobStorage`または`StorageV2`の Azure ストレージ アカウントで設定できます。 
  [データ レイク ストレージ Gen2 の](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)有効化もサポートされています。
* [イベント ハブ を作成する](https://docs.microsoft.com/azure/event-hubs/event-hubs-create):

### <a name="event-grid-subscription"></a>Event Grid サブスクリプション

* エンドポインド`Event Hub`型として選択された Kusto で、BLOB ストレージ イベント通知の転送に使用されます。 `Event Grid schema`は、通知用に選択されたスキーマです。 各偶数ハブは 1 つの接続を提供できることに注意してください。
* BLOB ストレージ サブスクリプション接続は、`Microsoft.Storage.BlobCreated`の種類の通知を処理します。 サブスクリプションを作成するときに、必ず選択してください。 他の種類の通知を選択すると、無視されることに注意してください。
* 1 つのサブスクリプションは、1 つ以上のコンテナー内のストレージ イベントに通知できます。 特定のコンテナからファイルを追跡する場合は、次のように通知のフィルタを設定します。 
   * **件名の開始フィルター**は、BLOB コンテナーの*リテラル*プレフィックスです。 適用されるパターンは *startswith* であるため、複数のコンテナーにまたがることができます。 ワイルドカードは使用できません。
     次のように設定*する必要があります*。 *`/blobServices/default/containers/<prefix>`* たとえば *、/blobServices/デフォルト/コンテナ/ストームイベント-2020-*
   * **[Subject Ends With]\(指定の値で終わる件名\)** フィールドは、BLOB の "*リテラル*" サフィックスです。 ワイルドカードは使用できません。 ファイル拡張子をフィルタリングする場合に便利です。

詳しい手順については、ストレージ アカウント ガイドの[「Event Grid サブスクリプションの作成方法」を参照してください](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-subscription-in-your-storage-account)。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure データ エクスプローラーへのデータ取り込み接続

* Azure ポータルを使用する: [Azure データ エクスプローラー でイベント グリッド データ接続を作成](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-data-connection-in-azure-data-explorer)します。
* KUSTO 管理 .NET SDK の使用:[イベント グリッド データ接続の追加](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-csharp#add-an-event-grid-data-connection)
* Kusto 管理 Python SDK を使用する:[イベント グリッド データ接続を追加する](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-python#add-an-event-grid-data-connection)
* ARM テンプレートを使用する場合:[イベント グリッド データ接続を追加するための Azure リソース マネージャー テンプレート](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-resource-manager#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>データの生成

> [!NOTE]
> * データ`BlockBlob`の生成に使用します。 `AppendBlob` はサポートされません。
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

ローカル ファイルから BLOB を作成し、取り込みプロパティを BLOB メタデータに設定してアップロードする例を次に示します。

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

## <a name="blob-lifecycle"></a>BLOB ライフサイクル

Azure データ エクスプローラーは、BLOB の取り込み後は削除しませんが、3 ~ 5 日間保持されます。 [Azure BLOB ストレージ のライフサイクル](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)を使用して、BLOB の削除を管理します。