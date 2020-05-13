---
title: Event Hub からの取り込み-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの Event Hub からの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: a9308fa762bf7bbda0f57a1c252cf2121253565a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373460"
---
# <a name="ingest-from-event-hub"></a>Event Hub からの取り込み

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about)は、ビッグデータストリーミングプラットフォームとイベントインジェストサービスです。 Azure データエクスプローラーは、お客様が管理する Event Hubs から継続的にインジェストを提供します。 

## <a name="data-format"></a>データ形式

* データは、 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)オブジェクトの形式でイベントハブから読み込まれます。
* イベントペイロードには、 [Azure データエクスプローラーでサポートされている](../../../ingestion-supported-formats.md)いずれかの形式で、取り込まれたされる1つ以上のレコードを含めることができます。
* 圧縮アルゴリズムを使用してデータを圧縮でき `GZip` ます。 インジェストプロパティとして指定する必要があり `Compression` [ingestion property](#ingestion-properties)ます。

> [!Note]
> * 圧縮形式 (Avro、Parquet、ORC) では、データ圧縮はサポートされていません。
> * カスタムエンコードおよび埋め込み[システムプロパティ](#event-system-properties-mapping)は、圧縮データではサポートされていません。

## <a name="ingestion-properties"></a>インジェストのプロパティ

インジェストプロパティはインジェストプロセスを指示します。 データをルーティングする場所とその処理方法。 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)を使用して、イベントインジェストの[インジェストプロパティ](../../../ingestion-properties.md)を指定できます。 以下のプロパティを設定できます。

|プロパティ |[説明]|
|---|---|
| テーブル | 既存の対象テーブルの名前 (大文字と小文字が区別されます)。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| Format | データ形式。 [`Data Connection`] ブレードでの [`Data format`] の設定をオーバーライドします。 |
| IngestionMappingReference | 使用する既存の[インジェストマッピング](../create-ingestion-mapping-command.md)の名前。 [`Data Connection`] ブレードでの [`Column mapping`] の設定をオーバーライドします。|
| 圧縮 | データ圧縮、 `None` (既定) または `GZip` 圧縮。|
| エンコード |  データエンコード、既定値は UTF8 です。 [.Net でサポートされているエンコーディング](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)のいずれかを指定できます。 |
| タグ (プレビュー) | JSON 配列文字列として書式設定された取り込まれたデータに関連付ける[タグ](../extents-overview.md#extent-tagging)の一覧。 タグを使用した場合のパフォーマンスへの[影響](../extents-overview.md#performance-notes-1)に注意してください。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>イベントのルーティング

Azure データエクスプローラークラスターへのイベントハブ接続を設定する場合は、ターゲットテーブルのプロパティ (テーブル名、データ形式、圧縮およびマッピング) を指定します。 これは、データの既定のルーティング (とも呼ばれ `static routing` ます) です。
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

システムプロパティは、イベントがエンキューされるときに、Event Hubs サービスによって設定されるプロパティを格納するために使用されるコレクションです。 Azure データエクスプローラーイベントハブ接続により、選択したプロパティがテーブルのデータランディングに埋め込まれます。

> [!Note]
> * システムプロパティは、単一レコードのイベントに対してサポートされています。
> * システムプロパティは、圧縮データではサポートされていません。
> * マッピングの場合 `csv` 、次の表に示す順序で、レコードの先頭にプロパティが追加されます。 マッピングの場合 `json` 、プロパティは次の表のプロパティ名に従って追加されます。

### <a name="event-hub-expose-the-following-system-properties"></a>イベントハブでは、次のシステムプロパティが公開されます。

|プロパティ |データ型 |説明|
|---|---|---|
| x-opt-enqueued-time |datetime | イベントがエンキューされた UTC 時刻。 |
| x-opt-sequence-number |long | イベントハブのパーティションストリーム内のイベントの論理シーケンス番号。
| x-opt-offset |string | イベントハブパーティションストリームを基準とするイベントのオフセット。 オフセット識別子は、イベントハブストリームのパーティション内で一意です。 |
| x-opt-パブリッシャー |string | メッセージがパブリッシャーエンドポイントに送信された場合のパブリッシャー名。 |
| x-opt-partition-key |string |イベントが格納されている、対応するパーティションのパーティションキー。 |

テーブルの [**データソース**] セクションで [**イベントシステムのプロパティ**] を選択した場合は、テーブルのスキーマとマッピングにプロパティを含める必要があります。

**テーブルスキーマの例**

データに3つの列 (、、および) が含まれており、含まれるプロパティがである場合は `Timespan` `Metric` `Value` `x-opt-enqueued-time` `x-opt-offset` 、次のコマンドを使用してテーブルスキーマを作成または変更します。

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV マッピングの例**

次のコマンドを実行して、レコードの先頭にデータを追加します。 注序数値: レコードの先頭に、上記の表に示されている順序でプロパティが追加されます。 これは、マップされているシステムプロパティに基づいて列序数が変化する CSV マッピングにとって重要です。

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

データは、[**データ接続**] ブレードの [**イベントシステムプロパティ**] の一覧に表示されるように、システムプロパティ名を使用して追加されます。 これらのコマンドを実行します。

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
> 最適なパフォーマンスを得るには、Azure データエクスプローラークラスターと同じリージョンにすべてのリソースを作成します。

### <a name="create-an-event-hub"></a>Event Hub を作成する

まだない場合は、[イベントハブを作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)します。 テンプレートは、「方法:[イベントハブを作成](../../../ingest-data-event-hub.md#create-an-event-hub)する」ガイドに記載されています。

> [!Note]
> * パーティションの数は変更できないため、設定については長期的な規模で検討する必要があります。
> * コンシューマーグループはコンシューマーごとに uniqe で*ある必要があり*ます。 Azure データエクスプローラー接続専用のコンシューマーグループを作成します。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure データエクスプローラーへのデータインジェスト接続

* Azure ポータルを使用し[て、イベントハブに接続](../../../ingest-data-event-hub.md#connect-to-the-event-hub)します。
* Azure データエクスプローラー management .NET SDK の使用:[イベントハブデータ接続を追加する](../../../data-connection-event-hub-csharp.md#add-an-event-hub-data-connection)
* Azure データエクスプローラー management Python SDK の使用:[イベントハブデータ接続を追加](../../../data-connection-event-hub-python.md#add-an-event-hub-data-connection)する
* ARM テンプレートを使用した場合:[イベントハブデータ接続を追加するための Azure Resource Manager テンプレート](../../../data-connection-event-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> **[データにルーティング情報が含ま**れる] が選択されている場合は、イベントのプロパティの一部として必要な[ルーティング](#events-routing)情報を指定*する必要があり*ます。

> [!Note]
> 接続が設定されると、作成時以降にエンキューされたイベントからデータを取り込みます。

#### <a name="generating-data"></a>データの生成

* データを生成してイベントハブに送信する[サンプルアプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)を参照してください。

イベントには、サイズ制限まで1つ以上のレコードを含めることができます。 次の例では、2つのイベントを送信します。それぞれに5個のレコードが追加されています。

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
