---
title: Event Hub からの取り込み-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの Event Hub からの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: dbb74d8b718b5a03fa0e9e7f4679f48c837d0842
ms.sourcegitcommit: c3bbb9a6bfd7c5506f05afb4968fdc2043a9fbbf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2020
ms.locfileid: "85332550"
---
# <a name="ingest-from-event-hub"></a>Event Hub からの取り込み

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about)は、ビッグデータストリーミングプラットフォームとイベントインジェストサービスです。 Azure データエクスプローラーは、お客様が管理する Event Hubs から継続的にインジェストを提供します。

## <a name="data-format"></a>データ形式

* データは、 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)オブジェクトの形式でイベントハブから読み込まれます。
* イベントペイロードには、 [Azure データエクスプローラーでサポートされ](../../../ingestion-supported-formats.md)ているいずれかの形式で取り込まれたされる1つ以上のレコードを含めることができます。
* 圧縮アルゴリズムを使用してデータを圧縮でき `GZip` ます。 インジェストプロパティとして指定する必要があり `Compression` [ingestion property](#ingestion-properties)ます。

> [!Note]
> * 圧縮形式 (Avro、Parquet、ORC) では、データ圧縮はサポートされていません。
> * カスタムエンコードおよび埋め込み[システムプロパティ](#event-system-properties-mapping)は、圧縮データではサポートされていません。

## <a name="ingestion-properties"></a>インジェストのプロパティ

インジェストのプロパティは、インジェストプロセス、データをルーティングする場所、およびその処理方法を指示します。 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)を使用して、イベントインジェストの[インジェストプロパティ](../../../ingestion-properties.md)を指定できます。 以下のプロパティを設定できます。

|プロパティ |[説明]|
|---|---|
| テーブル | 既存の対象テーブルの名前 (大文字と小文字が区別されます)。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| Format (形式) | データ形式。 [`Data Connection`] ブレードでの [`Data format`] の設定をオーバーライドします。 |
| IngestionMappingReference | 使用する既存の[インジェストマッピング](../create-ingestion-mapping-command.md)の名前。 [`Data Connection`] ブレードでの [`Column mapping`] の設定をオーバーライドします。|
| 圧縮 | データ圧縮、 `None` (既定)、または `GZip` 圧縮。|
| Encoding | データエンコード、既定値は UTF8 です。 [.Net でサポートされているエンコーディング](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)のいずれかを指定できます。 |
| タグ (プレビュー) | JSON 配列文字列として書式設定された取り込まれたデータに関連付ける[タグ](../extents-overview.md#extent-tagging)の一覧。 タグを使用する場合、[パフォーマンスに影響](../extents-overview.md#performance-notes-1)があります。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>イベントのルーティング

Azure データエクスプローラークラスターへのイベントハブ接続を設定する場合は、ターゲットテーブルのプロパティ (テーブル名、データ形式、圧縮、およびマッピング) を指定します。 データの既定のルーティングは、とも呼ばれ `static routing` ます。
イベントプロパティを使用して、各イベントのターゲットテーブルのプロパティを指定することもできます。 接続は、 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)で指定されているとおりにデータを動的にルーティングし、このイベントの静的プロパティをオーバーライドします。

次の例では、event hub の詳細を設定し、weather メトリックデータをテーブルに送信し `WeatherMetrics` ます。
データの `json` 形式はです。 `mapping1`はテーブルで事前定義されてい `WeatherMetrics` ます。

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

## <a name="event-system-properties-mapping"></a>イベント システム プロパティのマッピング

システムプロパティには、イベントがエンキューされるときに、Event Hubs サービスによって設定されるプロパティが格納されます。 Azure データエクスプローラーイベントハブ接続により、選択したプロパティがテーブルのデータランディングに埋め込まれます。

> [!Note]
> * システムプロパティは、単一レコードのイベントに対してサポートされています。
> * システムプロパティは、圧縮データではサポートされていません。
> * マッピングの場合 `csv` 、次の表に示す順序で、レコードの先頭にプロパティが追加されます。 マッピングの場合 `json` 、プロパティは次の表のプロパティ名に従って追加されます。

### <a name="event-hub-exposes-the-following-system-properties"></a>イベントハブは、次のシステムプロパティを公開します。

|プロパティ |データ型 |説明|
|---|---|---|
| x-opt-enqueued-time |DATETIME | イベントがエンキューされた UTC 時間 |
| x-opt-sequence-number |long | イベントハブのパーティションストリーム内のイベントの論理シーケンス番号
| x-opt-offset |string | イベントハブパーティションストリームからのイベントのオフセット。 オフセット識別子は、イベントハブストリームのパーティション内で一意です。 |
| x-opt-パブリッシャー |string | パブリッシャーのエンドポイントにメッセージが送信された場合の発行元の名前 |
| x-opt-partition-key |string |イベントが格納されている対応するパーティションのパーティションキー |

テーブルの [**データソース**] セクションで [**イベントシステムのプロパティ**] を選択した場合は、テーブルのスキーマとマッピングにプロパティを含める必要があります。

**テーブルスキーマの例**

次のデータが含まれている場合は、テーブルスキーマコマンドを使用してテーブルスキーマを作成または変更します。
* 列、 `Timespan` 、 `Metric` および`Value`  
* プロパティ `x-opt-enqueued-time` と`x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV マッピングの例**

次のコマンドを実行して、レコードの先頭にデータを追加します。
レコードの先頭に、上記の表に示されている順序でプロパティが追加されます。
序数値は、マップされているシステムプロパティに基づいて、列序数が変更される CSV マッピングにとって重要です。

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
 
**JSON マッピングの例**

[*データ接続*] ブレードの [*イベントシステムプロパティ*] の一覧に表示されるシステムプロパティ名を使用して、データを追加します。 次のコマンドを実行します。

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

## <a name="create-event-hub-connection"></a>イベントハブ接続の作成

> [!Note]
> 最適なパフォーマンスを得るには、Azure データエクスプローラークラスターと同じリージョンにすべてのリソースを作成します。

### <a name="create-an-event-hub"></a>イベント ハブの作成

まだない場合は、[イベントハブを作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)します。 テンプレートは、「方法:[イベントハブを作成](../../../ingest-data-event-hub.md#create-an-event-hub)する」ガイドに記載されています。

> [!Note]
> * パーティション数は変更できないため、パーティション数を設定する際には、長期的な規模を考慮する必要があります。
> * コンシューマーグループはコンシューマーごとに一意で*ある必要があり*ます。 Azure データエクスプローラー接続専用のコンシューマーグループを作成します。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure データエクスプローラーへのデータインジェスト接続

* Via Azure portal:[イベントハブに接続](../../../ingest-data-event-hub.md#connect-to-the-event-hub)します。
* Azure データエクスプローラー management .NET SDK を使用する:[イベントハブデータ接続を追加](../../../data-connection-event-hub-csharp.md#add-an-event-hub-data-connection)する
* Azure データエクスプローラー management Python SDK を使用する:[イベントハブデータ接続を追加](../../../data-connection-event-hub-python.md#add-an-event-hub-data-connection)する
* ARM テンプレートを使用した場合:[イベントハブデータ接続を追加するために Azure Resource Manager テンプレート](../../../data-connection-event-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-event-hub-data-connection)を使用する

> [!Note]
> **[データにルーティング情報が含ま**れる] が選択されている場合は、イベントのプロパティの一部として必要な[ルーティング](#events-routing)情報を指定*する必要があり*ます。

> [!Note]
> 接続が設定されると、作成時以降にエンキューされたイベントからデータが取り込みされます。

#### <a name="generating-data"></a>データを生成する

* データを生成してイベントハブに送信する[サンプルアプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)を参照してください。

イベントには、サイズ制限まで1つ以上のレコードを含めることができます。 次の例では、2つのイベントを送信します。それぞれに5つのレコードが追加されています。

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
