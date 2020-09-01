---
title: IoT Hub からの取り込み - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer での IoT Hub からの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 8a009c82f787dac0bd4a86209b07ffc14d9ec8cf
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874870"
---
# <a name="connect-to-iot-hub"></a>IoT Hub への接続

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) は、クラウド内でホストされているマネージド サービスであり、IoT アプリケーションとそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。 Azure Data Explorer は、[イベント ハブと互換性のある組み込みのエンドポイント](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)を使用して、お客様が管理する IoT ハブからの継続的インジェストを提供します。

IoT のインジェスト パイプラインでは、いくつかの手順が実行されます。 まず IoT Hub を作成し、このハブにデバイスを登録します。 次に、[特定の形式のデータ](#data-format)が取り込まれるターゲット テーブル Azure Data Explorer を、指定された[インジェスト プロパティ](#set-ingestion-properties)を使用して作成します。 IoT Hub 接続は、Azure Data Explorer テーブルに接続するために[イベント ルーティング](#set-events-routing)を認識している必要があります。 データは、[イベント システム プロパティのマッピング](#set-event-system-properties-mapping)に従って、選択したプロパティを使用して埋め込まれます。 このプロセスは、[Azure portal](ingest-data-iot-hub.md)、[C#](data-connection-iot-hub-csharp.md) または [Python](data-connection-iot-hub-python.md) によるプログラム、または [Azure Resource Manager テンプレート](data-connection-iot-hub-resource-manager.md)を使用して管理できます。


## <a name="create-iot-hub-connection"></a>IoT Hub 接続を作成する

> [!Note]
> 最適なパフォーマンスを得るには、Azure Data Explorer クラスターと同じリージョンにすべてのリソースを作成します。

IoT Hub をまだ用意していない場合は、[IoT Hub を作成します](ingest-data-iot-hub.md#create-an-iot-hub)。

> [!Note]
> * `device-to-cloud partitions` の数は変更できないため、パーティション数を設定するときは長期的な規模で検討する必要があります。
> * コンシューマー グループは、コンシューマーごとに一意である必要があります。 Azure Data Explorer 接続専用のコンシューマー グループを作成します。 Azure portal でリソースを検索し、`Built-in endpoints` にアクセスして新しいコンシューマー グループを追加します。

## <a name="data-format"></a>データ形式

* データは [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) オブジェクトの形式でイベント ハブ エンドポイントから読み取られます。
* イベント ペイロードは、[Azure Data Explorer でサポートされている形式](ingestion-supported-formats.md)のいずれかにすることができます。
* [サポートされる圧縮](ingestion-supported-formats.md#supported-data-compression-formats)を確認してください。
  元の非圧縮データ サイズは、BLOB メタデータの一部である必要があります。それ以外の場合は、Azure Data Explorer によって推定されます。 ファイルごとのインジェストの非圧縮サイズの制限は 4 GB です。  

## <a name="set-ingestion-properties"></a>インジェストのプロパティを設定する

インジェスト プロパティによってインジェスト プロセスが指示されます。 データをルーティングする場所とその処理方法です。 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) を使用して、イベント インジェストの[インジェスト プロパティ](ingestion-properties.md)を指定できます。 以下のプロパティを設定できます。

|プロパティ |説明|
|---|---|
| テーブル | 既存のターゲット テーブルの名前 (大文字と小文字の区別あり)。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| 形式 | データ形式。 [`Data Connection`] ブレードでの [`Data format`] の設定をオーバーライドします。 |
| IngestionMappingReference | 使用する既存の[インジェスト マッピング](kusto/management/create-ingestion-mapping-command.md)の名前。 [`Data Connection`] ブレードでの [`Column mapping`] の設定をオーバーライドします。|
| エンコード |  データ エンコード (既定値は UTF8)。 [.NET でサポートされているエンコード](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)のいずれかを指定できます。 |

## <a name="set-events-routing"></a>イベントのルーティングを設定する

Azure Data Explorer クラスターへの IoT Hub 接続を設定するときに、ターゲット テーブルのプロパティ (テーブル名、データ形式、およびマッピング) を指定します。 これはデータの既定のルーティングで、静的ルーティングと呼ばれることもあります。
イベント プロパティを使用して、各イベントのターゲット テーブルのプロパティを指定することもできます。 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties) の指定に従って、接続でデータが動的にルーティングされ、このイベントの静的プロパティがオーバーライドされます。

> [!Note]
> **[データにはルーティング情報が含まれています]** が選択されている場合は、必要なルーティング情報をイベントのプロパティの一部として指定する必要があります。

## <a name="set-event-system-properties-mapping"></a>イベント システム プロパティのマッピングを設定する

システム プロパティは、イベントの受信時に IoT Hub のサービスによって設定されるプロパティを格納するために使用されるコレクションです。 Azure Data Explorer IoT Hub 接続により、選択したプロパティがテーブルのデータ ランディングに埋め込まれます。

> [!Note]
> `csv` マッピングの場合、以下の表に示された順序でプロパティがレコードの先頭に追加されます。 `json` マッピングの場合、次の表のプロパティ名に従ってプロパティが追加されます。

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub では、次のシステム プロパティが公開されます。

|プロパティ |説明|
|---|---|
| message-id | 要求/応答パターンに使用する、メッセージのユーザー設定 ID。 |
| sequence-number | IoT Hub によって各 C2D メッセージに割り当てられる数値 (デバイスとキューごとに一意)。 |
| を | C2D メッセージで指定される宛先。 |
| absolute-expiry-time | メッセージの有効期限の日時。 |
| iothub-enqueuedtime | IoT Hub が Device-to-Cloud メッセージを受信した日時。 |
| correlation-id| 通常、要求/応答パターンで要求の MessageId を格納する、応答メッセージの文字列プロパティ。 |
| user-id| メッセージの送信元を指定するために使用される ID。 |
| iothub-ack| フィードバック メッセージのジェネレーター。 |
| iothub-connection-device-id| IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの deviceId が含まれます。 |
| iothub-connection-auth-generation-id| IoT Hub で D2C メッセージに対して設定される ID。 メッセージを送信したデバイスの connectionDeviceGenerationId (「デバイス ID のプロパティ」を参照) が含まれています。 |
| iothub-connection-auth-method| IoT Hub で D2C メッセージに対して設定される認証方法。 このプロパティには、メッセージを送信するデバイスの認証に使用する認証方法に関する情報が含まれます。 |

テーブルの **[データ ソース]** セクションで **[イベント システムのプロパティ]** を選択した場合は、テーブルのスキーマとマッピングにプロパティを含める必要があります。

### <a name="examples"></a>例 

#### <a name="table-schema-example"></a>テーブル スキーマの例

データに 3 つの列 (`Timespan`、`Metric`、および `Value`) が含まれており、含めるプロパティが `x-opt-enqueued-time` および `x-opt-offset` の場合は、次のコマンドを使用してテーブル スキーマを作成または変更します。

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

#### <a name="csv-mapping-example"></a>CSV マッピングの例

次のコマンドを実行して、レコードの先頭にデータを追加します。 序数値に注意してください。プロパティは、上の表に示された順序でレコードの先頭に追加されます。 これは、マップされているシステム プロパティに基づいて列序数が変化する CSV マッピングの場合に重要です。

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

データは、システム プロパティ名が **[データ接続]** ブレードの **[イベント システムのプロパティ]** 一覧に表示されるときに、システム プロパティを使用して追加されます。 これらのコマンドを実行します。

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

### <a name="generate-data"></a>データの生成

* デバイスをシミュレートし、データを生成する[サンプル プロジェクト](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device)を参照してください。

## <a name="next-steps"></a>次の手順

IoT Hub にデータを取り込むには、さまざまな方法があります。 各方法のチュートリアルについては、次のリンクを参照してください。

* [IoT Hub から Azure Data Explorer にデータを取り込む](ingest-data-iot-hub.md)
* [C# を使用して Azure Data Explorer 用に IoT Hub データ接続を作成する (プレビュー)](data-connection-iot-hub-csharp.md)
* [Python を使用して Azure Data Explorer 用に IoT Hub データ接続を作成する (プレビュー)](data-connection-iot-hub-python.md)
* [Azure Resource Manager テンプレートを使用して Azure Data Explorer 用に IoT Hub データ接続を作成する](data-connection-iot-hub-resource-manager.md)
