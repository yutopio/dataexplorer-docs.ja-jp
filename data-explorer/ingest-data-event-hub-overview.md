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
ms.openlocfilehash: 8b24d6f771eaa4004b36a1b3171eb593e2fc0f6c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874887"
---
# <a name="connect-to-event-hub"></a>イベント ハブへの接続


[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) は、ビッグ データのストリーミング プラットフォームとなるイベント インジェスト サービスです。 Azure Data Explorer は、お客様が管理する Event Hubs からの継続的なインジェストを提供します。

イベント ハブのインジェスト パイプラインは、いくつかのステップを実行して、Azure Data Explorer にイベントを転送します。 最初に、Azure portal でイベント ハブを作成します。 次に、[特定の形式のデータ](#data-format)が、指定された[インジェスト プロパティ](#set-ingestion-properties)を使用して取り込まれる Azure Data Explorer のターゲット テーブルを作成します。 イベント ハブ接続は[イベント ルーティング](#set-events-routing)を認識している必要があります。 データは、[イベント システム プロパティのマッピング](#set-event-system-properties-mapping)に従って、選択したプロパティを使用して埋め込まれます。 このプロセスは、[Azure portal](ingest-data-event-hub.md)、[C#](data-connection-event-hub-csharp.md) または [Python](data-connection-event-hub-python.md) によるプログラム、または [Azure Resource Manager テンプレート](data-connection-event-hub-resource-manager.md)を使用して管理できます。

## <a name="data-format"></a>データ形式

* データは [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) オブジェクトの形式でイベント ハブから読み取られます。
* イベント ペイロードは、[Azure Data Explorer でサポートされている形式](ingestion-supported-formats.md)のいずれかで取り込むことができます。
* `GZip` 圧縮アルゴリズムを使用してデータを圧縮できます。 `Compression` [インジェスト プロパティ](#set-ingestion-properties)として指定する必要があります。

    > [!Note]
    > * 圧縮形式 (Avro、Parquet、ORC) の場合はデータ圧縮はサポートされません。
    > * カスタム エンコードおよび埋め込み[システム プロパティ](#set-event-system-properties-mapping) は、圧縮データではサポートされていません。
    
## <a name="set-ingestion-properties"></a>インジェスト プロパティを設定する

インジェスト プロパティは、インジェスト プロセス、データのルーティング先と処理方法を指示します。 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) を使用して、イベント インジェストの[インジェスト プロパティ](ingestion-properties.md)を指定できます。 以下のプロパティを設定できます。

|プロパティ |説明|
|---|---|
| テーブル | 既存のターゲット テーブルの名前 (大文字と小文字の区別あり)。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| 形式 | データ形式。 [`Data Connection`] ブレードでの [`Data format`] の設定をオーバーライドします。 |
| IngestionMappingReference | 使用する既存の[インジェスト マッピング](kusto/management/create-ingestion-mapping-command.md)の名前。 [`Data Connection`] ブレードでの [`Column mapping`] の設定をオーバーライドします。|
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

### <a name="event-hub-exposes-the-following-system-properties"></a>イベント ハブでは、次のシステム プロパティが公開されます。

|プロパティ |データ型 |説明|
|---|---|---|
| x-opt-enqueued-time |DATETIME | イベントがエンキューされた UTC 時刻 |
| x-opt-sequence-number |long | イベント ハブのパーティション ストリーム内のイベントの論理シーケンス番号
| x-opt-offset |string | イベント ハブのパーティション ストリームからのイベントのオフセット。 このオフセット識別子は、イベント ハブ ストリームのパーティション内で一意です |
| x-opt-publisher |string | 発行元の名前 (発行元のエンドポイントにメッセージが送信された場合) |
| x-opt-partition-key |string |イベントが格納されている、対応するパーティションのパーティション キー |

テーブルの **[データ ソース]** セクションで **[イベント システムのプロパティ]** を選択した場合は、テーブルのスキーマとマッピングにプロパティを含める必要があります。

### <a name="examples"></a>例

#### <a name="table-schema-example"></a>テーブル スキーマの例

データに以下が含まれている場合は、テーブル スキーマ コマンドを使用してテーブル スキーマを作成または変更します。
* 列 `Timespan`、 `Metric`、および `Value`  
* プロパティ `x-opt-enqueued-time` および `x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

#### <a name="csv-mapping-example"></a>CSV マッピングの例

次のコマンドを実行して、レコードの先頭にデータを追加します。
上の表に示された順序でプロパティがレコードの先頭に追加されます。
序数値は、マップされているシステム プロパティに基づいて列序数が変化する CSV マッピングの場合に重要です。

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
#### <a name="json-mapping-example"></a>JSON マッピングの例

*[データ接続]* ブレードの *[イベント システムのプロパティ]* 一覧に表示されるとおりのシステム プロパティ名を使用してデータを追加します。 次のコマンドを実行します。

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```

## <a name="create-event-hub-connection"></a>イベント ハブ接続を作成する

> [!Note]
> 最適なパフォーマンスを得るには、Azure Data Explorer クラスターと同じリージョンにすべてのリソースを作成します。

### <a name="create-an-event-hub"></a>イベント ハブを作成する

まだ用意していない場合は、[イベント ハブを作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)します。 テンプレートは、使用法ガイド「[イベント ハブの作成](ingest-data-event-hub.md#create-an-event-hub)」にあります。

> [!Note]
> * パーティション数は変更できないため、パーティション数を設定する際には、長期的な規模を考慮する必要があります。
> * コンシューマー グループは、コンシューマーごとに一意である "*必要があります*"。 Azure Data Explorer 接続専用のコンシューマー グループを作成します。

#### <a name="generate-data"></a>データの生成

* データを生成してイベント ハブに送信する[サンプル アプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)をご覧ください。

イベントには、そのサイズ制限を上限として、1 つ以上のレコードを含めることができます。 次の例では、2 つのイベントを送信します。それぞれに 5 つのレコードが追加されています。

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```

## <a name="next-steps"></a>次の手順

* [イベント ハブから Azure Data Explorer にデータを取り込む](ingest-data-event-hub.md)
* [C# を使用して Azure Data Explorer 用にイベント ハブ データ接続を作成する](data-connection-event-hub-csharp.md)
* [Python を使用して Azure Data Explorer 用にイベント ハブ データ接続を作成する](data-connection-event-hub-python.md)
* [Azure Resource Manager テンプレートを使用して Azure Data Explorer 用に Event Hub データ接続を作成する](data-connection-event-hub-resource-manager.md)
