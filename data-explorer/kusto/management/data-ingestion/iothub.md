---
title: IoT Hub からの取り込み-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの IoT Hub からの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: c3ed0fa8608f2be5739c1143a648230f792e5d40
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373398"
---
# <a name="ingest-from-iot-hub"></a>IoT Hub からの取り込み

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)は、クラウドでホストされる管理されたサービスであり、IoT アプリケーションとそれが管理するデバイス間の双方向通信のための中央のメッセージハブとして機能します。 Azure データエクスプローラーは、 [Event hub と互換性のある組み込みエンドポイント](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)を使用して、お客様が管理する IoT hub から継続的にインジェストを提供します。

## <a name="data-format"></a>データ形式

* データは、 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)オブジェクトの形式でイベントハブエンドポイントから読み込まれます。
* イベントペイロードは、 [Azure データエクスプローラーでサポートされている](../../../ingestion-supported-formats.md)いずれかの形式にすることができます。

## <a name="ingestion-properties"></a>インジェストのプロパティ

インジェストプロパティはインジェストプロセスを指示します。 データをルーティングする場所とその処理方法。 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)を使用して、イベントインジェストの[インジェストプロパティ](../../../ingestion-properties.md)を指定できます。 以下のプロパティを設定できます。

|プロパティ |説明|
|---|---|
| テーブル | 既存の対象テーブルの名前 (大文字と小文字が区別されます)。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| Format | データ形式。 [`Data Connection`] ブレードでの [`Data format`] の設定をオーバーライドします。 |
| IngestionMappingReference | 使用する既存の[インジェストマッピング](../create-ingestion-mapping-command.md)の名前。 [`Data Connection`] ブレードでの [`Column mapping`] の設定をオーバーライドします。|
| エンコード |  データエンコード、既定値は UTF8 です。 [.Net でサポートされているエンコーディング](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)のいずれかを指定できます。 |

## <a name="events-routing"></a>イベントのルーティング

Azure データエクスプローラークラスターへの IoT Hub 接続を設定する場合は、ターゲットテーブルのプロパティ (テーブル名、データ形式、およびマッピング) を指定します。 これは、データの既定のルーティング (とも呼ばれ `static routing` ます) です。
イベントプロパティを使用して、各イベントのターゲットテーブルのプロパティを指定することもできます。 接続は、 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)で指定されているとおりにデータを動的にルーティングし、このイベントの静的プロパティをオーバーライドします。

## <a name="event-system-properties-mapping"></a>イベント システム プロパティのマッピング

システムプロパティは、イベントの受信時に IoT hub サービスによって設定されるプロパティを格納するために使用されるコレクションです。 Azure データエクスプローラー IoT Hub 接続により、選択したプロパティがテーブルのデータランディングに埋め込まれます。

> [!Note]
> * マッピングの場合 `csv` 、次の表に示す順序で、レコードの先頭にプロパティが追加されます。 マッピングの場合 `json` 、プロパティは次の表のプロパティ名に従って追加されます。

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub は、次のシステムプロパティを公開します。

|プロパティ |説明|
|---|---|
| message-id | 要求/応答パターンに使用する、メッセージのユーザー設定 ID。 |
| sequence-number | IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 |
| to | C2D メッセージで指定される宛先。 |
| absolute-expiry-time | メッセージの有効期限の日時。 |
| iothub-enqueuedtime | IoT Hub が Device-to-Cloud メッセージを受信した日時。 |
| correlation-id| 通常、要求/応答パターンで要求の MessageId を格納する、応答メッセージの文字列プロパティ。 |
| user-id| メッセージの送信元を指定するために使用される ID。 |
| iothub-ack| フィードバック メッセージのジェネレーター。 |
| iothub-connection-device-id| IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの deviceId が含まれます。 |
| iothub-connection-auth-generation-id| IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの connectionDeviceGenerationId (「デバイス ID のプロパティ」を参照) が含まれています。 |
| iothub-connection-auth-method| IoT Hub で D2C メッセージに対して設定される認証方法。 このプロパティには、メッセージを送信するデバイスの認証に使用する認証方法に関する情報が含まれます。 |

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

## <a name="create-iot-hub-connection"></a>IoT Hub 接続の作成

> [!Note]
> 最適なパフォーマンスを得るには、Azure データエクスプローラークラスターと同じリージョンにすべてのリソースを作成します。

### <a name="create-an-iot-hub"></a>IoT Hub の作成

[Iot hub](../../../ingest-data-iot-hub.md#create-an-iot-hub)をまだ作成していない場合は、作成します。

> [!Note]
> * `device-to-cloud partitions`カウントは変更できないため、パーティション数を設定するときは、長期的なスケールを考慮する必要があります。
> * コンシューマーグループはコンシューマーごとに uniqe で*ある必要があり*ます。 Kusto 接続専用のコンシューマーグループを作成します。 Azure Portal でリソースを検索し、「」に進んで、 `Built-in endpoints` 新しいコンシューマーグループを追加します。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure データエクスプローラーへのデータインジェスト接続

* Azure ポータルを使用して[、azure データエクスプローラーテーブルを IoT hub に接続](../../../ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub)します。
* Azure データエクスプローラー management .NET SDK の使用: [IoT Hub データ接続を追加する](../../../data-connection-iot-hub-csharp.md#add-an-iot-hub-data-connection)
* Azure データエクスプローラー management Python SDK の使用: [IoT Hub データ接続を追加する](../../../data-connection-iot-hub-python.md#add-an-iot-hub-data-connection)
* ARM テンプレートを使用した場合: [Iot hub データ接続を追加するための Azure Resource Manager テンプレート](../../../data-connection-iot-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> **[データにルーティング情報が含ま**れる] が選択されている場合は、イベントのプロパティの一部として必要な[ルーティング](#events-routing)情報を指定*する必要があり*ます。

> [!Note]
> 接続が設定されると、作成時以降にエンキューされたイベントからデータを取り込みます。

### <a name="generating-data"></a>データの生成

* デバイスをシミュレートし、データを生成する[サンプルプロジェクト](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device)を参照してください。