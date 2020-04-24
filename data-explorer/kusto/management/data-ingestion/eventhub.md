---
title: イベント ハブからの取り込み - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのイベント ハブからの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ddb218e707152f529e5b547ffe06c4d3c7614811
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521507"
---
# <a name="ingest-from-event-hub"></a>イベント ハブからの取り込み

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about)は、ビッグ データ ストリーミング プラットフォームとイベント インジェスト サービスです。 Azure データ エクスプローラーでは、お客様が管理する Event Hubs から継続的に取り込みができます。 

## <a name="data-format"></a>データ形式

* データは[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)オブジェクトの形式でイベント ハブから読み取られます。
* イベント ペイロードには[、Azure Data Explorer でサポートされるいずれかの形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)で、取り込む 1 つ以上のレコードを含めることができます。
* 圧縮アルゴリズムを使用してデータ`GZip`を圧縮できます。 `Compression`[インジェスト プロパティ](#ingestion-properties)として指定する必要があります。

> [!Note]
> * 圧縮形式 (Avro、Parquet、ORC) では、データ圧縮はサポートされていません。
> * カスタム エンコードおよび埋め込み[システム プロパティ](#event-system-properties-mapping)は、圧縮データではサポートされていません。

## <a name="ingestion-properties"></a>インジェストのプロパティ

取り込みプロパティは、取り込みプロセスを指示します。 データのルーティング場所と処理方法。 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)を使用して、イベント インジェクションの取り込み[プロパティ](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)を指定できます。 以下のプロパティを設定できます。

|プロパティ |説明|
|---|---|
| テーブル | 既存のターゲット表の名前 (大文字と小文字を区別する)。 ブレードの`Table`セットを上書`Data Connection`きします。 |
| Format | データ形式。 ブレードの`Data format`セットを上書`Data Connection`きします。 |
| インジェスティションマッピングリファレンス | 使用する既存の[取り込みマッピング](../create-ingestion-mapping-command.md)の名前。 ブレードの`Column mapping`セットを上書`Data Connection`きします。|
| 圧縮 | データ圧縮、(`None`デフォルト)または`GZip`圧縮。|
| エンコード |  データエンコーディングのデフォルトは UTF8 です。 [NET でサポートされる任意のエンコーディングを](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)使用できます。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>イベントルーティング

Azure Data Explorer クラスターへのイベント ハブ接続を設定する際には、ターゲット テーブルのプロパティ (テーブル名、データ形式、圧縮、マッピング) を指定します。 これは、データの既定のルーティングです`static routing`。
イベントプロパティを使用して、各イベントのターゲットテーブルプロパティを指定することもできます。 接続は、このイベントの静的プロパティをオーバーライドするイベントのプロパティをオーバーライドする[イベントのプロパティ](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)で指定されたとおりにデータを動的にルーティングします。

次のサンプルでは、イベント ハブの詳細を設定し、気象メトリック`WeatherMetrics`データをテーブル に送信します。
データは形式`json`です。 `mapping1`は、テーブル`WeatherMetrics`で事前定義されています。

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

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>イベント システム プロパティのマッピング

システム プロパティは、イベントがキューに入れられている時間に Event Hubs サービスによって設定されたプロパティを格納するために使用されるコレクションです。 Azure データ エクスプローラー のイベント ハブ接続は、選択したプロパティをテーブルのデータ ランディングに埋め込みます。

> [!Note]
> * システム プロパティは、単一レコード イベントでサポートされています。
> * 圧縮データではシステム プロパティはサポートされていません。
> * マッピング`csv`の場合、プロパティは次の表に示す順序でレコードの先頭に追加されます。 マッピング`json`の場合、プロパティは次の表のプロパティ名に従って追加されます。

### <a name="event-hub-expose-the-following-system-properties"></a>イベント ハブは、次のシステム プロパティを公開します。

|プロパティ |データ型 |説明|
|---|---|---|
| x-opt-enqueued-time |DATETIME | イベントがキューに入れられていた UTC 時間。 |
| x-opt-sequence-number |long | イベント ハブのパーティション ストリーム内のイベントの論理シーケンス番号。
| x-opt-offset |string | イベント ハブパーティション ストリームに対するイベントのオフセット。 オフセット識別子は、イベント ハブ ストリームのパーティション内で一意です。 |
| x-オプトパブリッシャー |string | メッセージがパブリッシャー エンドポイントに送信された場合のパブリッシャー名。 |
| x-opt-partition-key |string |イベントを格納した対応するパーティションのパーティション キー。 |

テーブルの **[データ ソース**] セクションで **[イベント システムのプロパティ**] を選択した場合は、テーブル スキーマとマッピングにプロパティを含める必要があります。

**テーブル スキーマの例**

データに 3 つの列`Timespan` `Metric`( `Value`、 、および )`x-opt-enqueued-time`が`x-opt-offset`含まれている場合、次のコマンドを使用してテーブル スキーマを作成または変更します。

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV マッピングの例**

レコードの先頭にデータを追加するには、次のコマンドを実行します。 注: プロパティは、上の表に示した順序でレコードの先頭に追加されます。 これは、マッピングされるシステムプロパティに基づいて列序数が変更される CSV マッピングにとって重要です。

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

データは、[データ**接続**] ブレードの **[イベント システムのプロパティ]** ボックスに表示されるシステム プロパティ名を使用して追加されます。 次の各コマンドを実行します。

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
> 最適なパフォーマンスを得るため、Azure データ エクスプローラー クラスターと同じリージョン内にすべてのリソースを作成します。

### <a name="create-an-event-hub"></a>Event Hub を作成する

まだ存在しない場合は、[イベント ハブ を作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)します。 テンプレートは、[イベント ハブの作成](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub)に関するガイドを参照してください。

> [!Note]
> * パーティションの数は変更できないため、設定については長期的な規模で検討する必要があります。
> * 消費者グループは、消費者ごとにユニクセである*必要があります*。 Azure データ エクスプローラー接続専用のコンシューマー グループを作成します。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure データ エクスプローラーへのデータ取り込み接続

* Azure ポータル経由:[イベント ハブ に接続](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)します。
* Azure データ エクスプローラー管理の .NET SDK の使用:[イベント ハブデータ接続の追加](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* Azure データ エクスプローラー管理の使用 Python SDK:[イベント ハブ データ接続の追加](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* ARM テンプレートを使用する場合:[イベント ハブ データ接続を追加するための Azure リソース マネージャー テンプレート](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> **[マイ データ] でルーティング情報**が選択されている場合は、イベント プロパティの一部として必要な[ルーティング](#events-routing)情報を指定する*必要があります*。

> [!Note]
> 接続が設定されると、作成後にキューに入れられたイベントから開始するデータを取り込みます。

#### <a name="generating-data"></a>データの生成

* データを生成してイベント ハブに送信する[サンプル アプリ](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)を参照してください。

イベントには、サイズ制限に達した 1 つ以上のレコードを含めることができます。 次のサンプルでは、2 つのイベントを送信します。

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