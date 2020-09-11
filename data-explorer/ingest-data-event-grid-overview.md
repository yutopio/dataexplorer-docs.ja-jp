---
title: Event Grid サブスクリプションを使用したストレージからの取り込み - Azure Data Explorer
description: この記事では、Azure Data Explorer での Event Grid サブスクリプションを使用したストレージからの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 4ef0365336c1c4e04d19acbb14e2b6d1ec4cdd82
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502689"
---
# <a name="create-a-connection-to-event-grid"></a>Event Grid への接続を作成する

Event Grid は、Azure ストレージをリッスンし、サブスクライブしたイベントが発生したときに情報をプルするように Azure Data Explorer を更新するパイプラインです。 Azure Data Explorer は、BLOB 作成通知の [Azure Event Grid](/azure/event-grid/overview) サブスクリプションを使用した Azure Storage (BLOB ストレージと ADLSv2) からの継続的なインジェストと、これらの通知のイベント ハブを介した Azure Data Explorer へのストリーミングを提供します。

Event Grid のインジェスト パイプラインでは、いくつかの手順が実行されます。 [特定の形式のデータ](#data-format)が取り込まれるターゲット テーブルを Azure Data Explorer に作成します。 次に、Azure Data Explorer で Event Grid データ接続を作成します。 Event Grid データ接続では、データを送信するテーブルやテーブルのマッピングなど、[イベントのルーティング](#set-events-routing)情報を把握している必要があります。 また、取り込まれるデータ、ターゲット テーブル、およびマッピングを記述する[インジェスト プロパティ](#set-ingestion-properties)も指定します。 サンプル データを生成し、[BLOB にアップロード](#upload-blobs)して、接続をテストすることができます。 インジェスト後、[BLOB を削除](#delete-blobs-using-storage-lifecycle)します。 このプロセスは、[Azure portal](ingest-data-event-grid.md)、[C#](data-connection-event-grid-csharp.md) または [Python](data-connection-event-grid-python.md) によるプログラム、または [Azure Resource Manager テンプレート](data-connection-event-grid-resource-manager.md)を使用して管理できます。

Azure Data Explorer でのデータ インジェストに関する一般的な情報については、「[Azure Data Explorer のデータ インジェスト概要](ingest-data-overview.md)」を参照してください。

## <a name="data-format"></a>データ形式

* [サポートされる形式](ingestion-supported-formats.md)を確認してください。
* [サポートされる圧縮](ingestion-supported-formats.md#supported-data-compression-formats)を確認してください。
    * 元の非圧縮データ サイズは、BLOB メタデータの一部である必要があります。それ以外の場合は、Azure Data Explorer によって推定されます。 ファイルごとのインジェストの非圧縮サイズの制限は 4 GB です。

## <a name="set-ingestion-properties"></a>インジェストのプロパティを設定する

BLOB メタデータを使用して、BLOB インジェストの[インジェストのプロパティ](ingestion-properties.md)を指定できます。
以下のプロパティを設定できます。

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## <a name="set-events-routing"></a>イベントのルーティングを設定する

Azure Data Explorer クラスターへの BLOB ストレージ接続を設定するときに、ターゲット テーブルのプロパティを指定します。
* テーブル名
* データ形式
* mapping

この設定は、データの既定のルーティングです (`static routing` と呼ばれることもあります)。
BLOB メタデータを使用して、各 BLOB のターゲット テーブルのプロパティを指定することもできます。 データは、[インジェスト プロパティ](#set-ingestion-properties)の指定に従って、動的にルーティングされます。

次の例は、BLOB メタデータをアップロードする前にインジェスト プロパティを設定する方法を示しています。 BLOB は異なるテーブルにルーティングされます。

詳細については、「[BLOB をアップロードする](#upload-blobs)」を参照してください。

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="upload-blobs"></a>BLOB をアップロードする

ローカル ファイルから BLOB を作成し、インジェストのプロパティを BLOB メタデータに設定して、それをアップロードすることができます。 例については、「[Event Grid の通知をサブスクライブすることで Azure Data Explorer に BLOB を取り込む](ingest-data-event-grid.md#generate-sample-data)」のページを参照してください。

> [!NOTE]
> `BlockBlob` を使用してデータを生成します。 `AppendBlob` がサポートされていません。

> [!NOTE]
> Azure Data Lake Gen2 ストレージの SDK を使用するには、ファイルのアップロードに `CreateFile` を使用し、最後の `Flush` で close パラメーターを「true」に設定する必要があります。
> Data Lake Gen2 SDK の正しい使用方法の詳細な例については、「[Azure Data Lake SDK を使用してファイルをアップロードする](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk)」を参照してください。

## <a name="delete-blobs-using-storage-lifecycle"></a>ストレージ ライフサイクルを使用した BLOB の削除

Azure Data Explorer では、取り込み後に BLOB は削除されません。 [Azure Blob Storage のライフサイクル](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)を使用して、BLOB の削除を管理してください。 BLOB は 3 日から 5 日間保持することをお勧めします。

## <a name="known-event-grid-issues"></a>Event Grid の既知の問題

* Azure Data Explorer を使用して、Event Grid のインジェストに使用されるファイルを[エクスポート](kusto/management/data-export/export-data-to-storage.md)する場合は、次の点に注意してください。 
    * エクスポート コマンドに指定された接続文字列、または[外部テーブル](kusto/management/data-export/export-data-to-an-external-table.md)に指定された接続文字列が [ADLS Gen2 形式](kusto/api/connection-strings/storage.md#azure-data-lake-store)の接続文字列 (`abfss://filesystem@accountname.dfs.core.windows.net`など) であるが、ストレージ アカウントが階層型名前空間に対して有効になっていない場合、Event Grid 通知はトリガーされません。
    * アカウントが階層型名前空間に対して有効でない場合、接続文字列で [Blob Storage](kusto/api/connection-strings/storage.md#azure-storage-blob) 形式 (たとえば、`https://accountname.blob.core.windows.net`) を使用する必要があります。 ADLS Gen2 接続文字列を使用している場合でもエクスポートは想定どおりに動作しますが、通知はトリガーされず、Event Grid インジェストは機能しません。

## <a name="next-steps"></a>次の手順

* [Event Grid の通知をサブスクライブすることで Azure Data Explorer に BLOB を取り込む](ingest-data-event-grid.md)
* [C# を使用して Azure Data Explorer 用にイベント ハブ データ接続を作成する](data-connection-event-hub-csharp.md)
* [Python を使用して Azure Data Explorer 用に Event Grid データ接続を作成する](data-connection-event-grid-python.md)
* [Azure Resource Manager テンプレートを使用して Azure Data Explorer 用に Event Grid データ接続を作成する](data-connection-event-grid-resource-manager.md)
