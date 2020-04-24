---
title: IoT ハブからの取り込み - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの IoT Hub からの取り込みについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 33b6f4f737657ee0a6c2712f3b7cadce0c9c0a8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521354"
---
# <a name="ingest-from-iot-hub"></a>IoT ハブからの取り込み

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)は、クラウドでホストされるマネージド サービスであり、IoT アプリケーションと管理するデバイスとの間の双方向通信の中心的なメッセージ ハブとして機能します。 Azure データ エクスプローラーは[、Event Hub と互換性のあるエンドポイント](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)を使用して、顧客管理の IoT Hub から継続的に取り込まれます。

## <a name="data-format"></a>データ形式

* データは[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)オブジェクトの形式でイベント ハブ エンドポイントから読み取られます。
* イベント ペイロードは[、Azure データ エクスプローラー でサポートされるいずれかの形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)で指定できます。

## <a name="ingestion-properties"></a>インジェストのプロパティ

取り込みプロパティは、取り込みプロセスを指示します。 データのルーティング場所と処理方法。 [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)を使用して、イベント インジェクションの取り込み[プロパティ](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)を指定できます。 以下のプロパティを設定できます。

|プロパティ |説明|
|---|---|
| テーブル | 既存のターゲット表の名前 (大文字と小文字を区別する)。 ブレードの`Table`セットを上書`Data Connection`きします。 |
| Format | データ形式。 ブレードの`Data format`セットを上書`Data Connection`きします。 |
| インジェスティションマッピングリファレンス | 使用する既存の[取り込みマッピング](../create-ingestion-mapping-command.md)の名前。 ブレードの`Column mapping`セットを上書`Data Connection`きします。|
| エンコード |  データエンコーディングのデフォルトは UTF8 です。 [NET でサポートされる任意のエンコーディングを](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)使用できます。 |

## <a name="events-routing"></a>イベントルーティング

Azure データ エクスプローラー クラスターへの IoT Hub 接続を設定する際には、ターゲット テーブルのプロパティ (テーブル名、データ形式、マッピング) を指定します。 これは、データの既定のルーティングです`static routing`。
イベントプロパティを使用して、各イベントのターゲットテーブルプロパティを指定することもできます。 接続は、このイベントの静的プロパティをオーバーライドするイベントのプロパティをオーバーライドする[イベントのプロパティ](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)で指定されたとおりにデータを動的にルーティングします。

## <a name="event-system-properties-mapping"></a>イベント システム プロパティのマッピング

システム プロパティは、イベントを受信した時点で IoT Hubs サービスによって設定されたプロパティを格納するために使用されるコレクションです。 Azure データ エクスプローラー IoT Hub 接続は、選択したプロパティをテーブルのデータ ランディングに埋め込みます。

> [!Note]
> * マッピング`csv`の場合、プロパティは次の表に示す順序でレコードの先頭に追加されます。 マッピング`json`の場合、プロパティは次の表のプロパティ名に従って追加されます。

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub は、次のシステム プロパティを公開します。

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

## <a name="create-iot-hub-connection"></a>IoT ハブ接続の作成

> [!Note]
> 最適なパフォーマンスを得るため、Azure データ エクスプローラー クラスターと同じリージョン内にすべてのリソースを作成します。

### <a name="create-an-iot-hub"></a>IoT Hub の作成

まだ持っていない場合は[、IoT Hub を作成](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#create-an-iot-hub)します。

> [!Note]
> * カウント`device-to-cloud partitions`は変更できないので、パーティション数を設定する場合は長期的なスケールを考慮する必要があります。
> * 消費者グループは、消費者ごとにユニクセである*必要があります*。 Kusto 接続専用のコンシューマー グループを作成します。 Azure ポータルでリソースを探し、新`Built-in endpoints`しいコンシューマー グループを追加します。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure データ エクスプローラーへのデータ取り込み接続

* Azure ポータル経由: [Azure データ エクスプローラー テーブルを IoT ハブ に接続](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#connect-azure-data-explorer-table-to-iot-hub)します。
* Azure データ エクスプローラー管理 .NET SDK の使用: [IoT Hub データ接続を追加する](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-csharp#add-an-iot-hub-data-connection)
* Azure データ エクスプローラー管理の使用 Python SDK: [IoT Hub データ接続を追加する](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-python#add-an-iot-hub-data-connection)
* ARM テンプレートを使用する場合: [Iot Hub データ接続を追加するための Azure リソース マネージャー テンプレート](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-resource-manager#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> **[マイ データ] でルーティング情報**が選択されている場合は、イベント プロパティの一部として必要な[ルーティング](#events-routing)情報を指定する*必要があります*。

> [!Note]
> 接続が設定されると、作成後にキューに入れられたイベントから開始するデータを取り込みます。

### <a name="generating-data"></a>データの生成

* デバイスをシミュレートしてデータを生成する[サンプル プロジェクト](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device)を参照してください。