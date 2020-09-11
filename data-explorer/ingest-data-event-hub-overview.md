---
title: イベント ハブからの取り込み - Azure Data Explorer
description: この記事では、Azure Data Explorer でのイベント ハブからの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: b3a8c379ad010a9787fdb8b7d4e2961fb58ead9e
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502665"
---
# <a name="create-a-connection-to-event-hub"></a>イベント ハブへの接続を作成する

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) は、ビッグ データのストリーミング プラットフォームとなるイベント インジェスト サービスです。 Azure Data Explorer は、お客様が管理する Event Hubs からの継続的なインジェストを提供します。

イベント ハブのインジェスト パイプラインは、いくつかのステップで、Azure Data Explorer にイベントを転送します。 最初に、Azure portal でイベント ハブを作成します。 次に、[特定の形式のデータ](#data-format)が、指定された[インジェスト プロパティ](#set-ingestion-properties)を使用して取り込まれるターゲット テーブルを Azure Data Explorer に作成します。 イベント ハブ接続は[イベント ルーティング](#set-events-routing)を認識している必要があります。 データは、[イベント システム プロパティのマッピング](#set-event-system-properties-mapping)に従って、選択したプロパティを使用して埋め込まれます。 イベント ハブへの[接続を作成](#create-event-hub-connection)して、[イベント ハブを作成](#create-an-event-hub)し、[イベントを送信](#send-events)します。 このプロセスは、[Azure portal](ingest-data-event-hub.md)、[C#](data-connection-event-hub-csharp.md) または [Python](data-connection-event-hub-python.md) によるプログラム、または [Azure Resource Manager テンプレート](data-connection-event-hub-resource-manager.md)を使用して管理できます。

Azure Data Explorer でのデータ インジェストに関する一般的な情報については、「[Azure Data Explorer のデータ インジェスト概要](ingest-data-overview.md)」を参照してください。

## <a name="data-format"></a>データ形式

* データは [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) オブジェクトの形式でイベント ハブから読み取られます。
* [サポートされる形式](ingestion-supported-formats.md)を確認してください。
    > [!NOTE]
    > イベント ハブでは、.raw 形式はサポートされていません。

* [サポートされる圧縮](ingestion-supported-formats.md#supported-data-compression-formats)を確認してください。
   * 圧縮形式 (Avro、Parquet、ORC) については、データ圧縮はサポートされません。
   * カスタム エンコードおよび埋め込み[システム プロパティ](#set-event-system-properties-mapping)は、圧縮データではサポートされていません。
  
## <a name="set-ingestion-properties"></a>インジェスト プロパティを設定する

インジェスト プロパティは、インジェスト プロセス、データのルーティング先と処理方法を指示します。 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) を使用して、イベント インジェストの[インジェスト プロパティ](ingestion-properties.md)を指定できます。 以下のプロパティを設定できます。

|プロパティ |説明|
|---|---|
| テーブル | 既存のターゲット テーブルの名前 (大文字と小文字の区別あり)。 [`Data Connection`] ペインで設定された `Table` をオーバーライドします。 |
| 形式 | データ形式。 [`Data Connection`] ペインで設定された `Data format` をオーバーライドします。 |
| IngestionMappingReference | 使用する既存の[インジェスト マッピング](kusto/management/create-ingestion-mapping-command.md)の名前。 [`Data Connection`] ペインで設定された `Column mapping` をオーバーライドします。|
| 圧縮 | データ圧縮、`None` (既定)、または `GZip` 圧縮。|
| エンコード | データ エンコード (既定値は UTF8)。 [.NET でサポートされているエンコード](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)のいずれかを指定できます。 |
| タグ (プレビュー) | JSON 配列文字列として書式設定された、取り込まれたデータに関連付けられる[タグ](kusto/management/extents-overview.md#extent-tagging)の一覧。 タグを使用すると、[パフォーマンスに影響](kusto/management/extents-overview.md#performance-notes-1)します。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="set-events-routing"></a>イベントのルーティングを設定する

Azure Data Explorer クラスターへのイベント ハブ接続を設定するときに、ターゲット テーブルのプロパティ (テーブル名、データ形式、圧縮、およびマッピング) を指定します。 データの既定のルーティングは、`static routing` と呼ばれることもあります。
イベント プロパティを使用して、各イベントのターゲット テーブルのプロパティを指定することもできます。 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) の指定に従って、接続でデータが動的にルーティングされ、このイベントの静的プロパティがオーバーライドされます。

次の例では、イベント ハブの詳細を設定し、気象メトリック データをテーブル `WeatherMetrics` に送信します。
データは `json` 形式です。 `mapping1` はテーブル `WeatherMetrics` で事前定義されています。

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="set-event-system-properties-mapping"></a>イベント システム プロパティのマッピングを設定する

イベントがエンキューされるときに、Event Hubs サービスによって設定されたプロパティがシステム プロパティに格納されます。 Azure Data Explorer イベント ハブ接続により、選択したプロパティがテーブルのデータ ランディングに埋め込まれます。

> [!Note]
> * システム プロパティは、単一レコードのイベントに対してサポートされています。
> * システム プロパティは、圧縮データではサポートされていません。
> * `csv` マッピングの場合、以下の表に示された順序でプロパティがレコードの先頭に追加されます。 `json` マッピングの場合、次の表のプロパティ名に従ってプロパティが追加されます。

### <a name="system-properties"></a>システム プロパティ

イベント ハブでは、次のシステム プロパティが公開されます。

|プロパティ |データ型 |説明|
|---|---|---|
| x-opt-enqueued-time |DATETIME | イベントがエンキューされた UTC 時刻 |
| x-opt-sequence-number |long | イベント ハブのパーティション ストリーム内のイベントの論理シーケンス番号
| x-opt-offset |string | イベント ハブのパーティション ストリームからのイベントのオフセット。 このオフセット識別子は、イベント ハブ ストリームのパーティション内で一意です |
| x-opt-publisher |string | 発行元の名前 (発行元のエンドポイントにメッセージが送信された場合) |
| x-opt-partition-key |string |イベントが格納されている、対応するパーティションのパーティション キー |

テーブルの **[データ ソース]** セクションで **[イベント システムのプロパティ]** を選択した場合は、テーブルのスキーマとマッピングにプロパティを含める必要があります。

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="create-event-hub-connection"></a>イベント ハブ接続を作成する

> [!Note]
> 最適なパフォーマンスを得るには、Azure Data Explorer クラスターと同じリージョンにすべてのリソースを作成します。

### <a name="create-an-event-hub"></a>イベント ハブを作成する

まだ用意していない場合は、[イベント ハブを作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)します。 イベント ハブへの接続は、[Azure portal](ingest-data-event-hub.md)、[C#](data-connection-event-hub-csharp.md) または [Python](data-connection-event-hub-python.md) によるプログラム、または [Azure Resource Manager テンプレート](data-connection-event-hub-resource-manager.md)を使用して管理できます。


> [!Note]
> * パーティション数は変更できないため、パーティション数を設定する際には、長期的な規模を考慮する必要があります。
> * コンシューマー グループは、コンシューマーごとに一意である "*必要があります*"。 Azure Data Explorer 接続専用のコンシューマー グループを作成します。

## <a name="send-events"></a>送信イベント

データを生成してイベント ハブに送信する[サンプル アプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)をご覧ください。

サンプル データの生成方法の例については、「[イベント ハブから Azure Data Explorer にデータを取り込む](ingest-data-event-hub.md#generate-sample-data)」を参照してください。

## <a name="next-steps"></a>次の手順

* [イベント ハブから Azure Data Explorer にデータを取り込む](ingest-data-event-hub.md)
* [C# を使用して Azure Data Explorer 用にイベント ハブ データ接続を作成する](data-connection-event-hub-csharp.md)
* [Python を使用して Azure Data Explorer 用にイベント ハブ データ接続を作成する](data-connection-event-hub-python.md)
* [Azure Resource Manager テンプレートを使用して Azure Data Explorer 用に Event Hub データ接続を作成する](data-connection-event-hub-resource-manager.md)
