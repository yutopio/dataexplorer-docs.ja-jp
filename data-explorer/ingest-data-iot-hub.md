---
title: IoT Hub から Azure Data Explorer にデータを取り込む
description: この記事では、IoT Hub から Azure Data Explorer にデータを取り込む (読み込む) 方法を学習します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/08/2020
ms.openlocfilehash: 47fce36f598c334c5e372ccb7bc44d21bd9ff94f
ms.sourcegitcommit: 97404e9ed4a28cd497d2acbde07d00149836d026
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832794"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>IoT Hub から Azure Data Explorer にデータを取り込む 

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager テンプレート](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

この記事では、ビッグ データのストリーミング プラットフォームとなる IoT インジェスト サービスである IoT Hub から Azure Data Explorer にデータを取り込む方法について説明します。

IoT Hub から Azure Data Explorer への取り込みに関する一般的な情報については、[IoT Hub への接続](ingest-data-iot-hub-overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* *testdb* というデータベース名の[テスト クラスターとデータベース](create-cluster-database-portal.md)を作成します。
* デバイスをシミュレートする[サンプル アプリ](https://github.com/Azure-Samples/azure-iot-samples-csharp)とドキュメントです。
* サンプル アプリを実行するための [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)。

## <a name="create-an-iot-hub"></a>IoT ハブを作成する

[!INCLUDE [iot-hub-include-create-hub](includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>IoT Hub にデバイスを登録する

[!INCLUDE [iot-hub-get-started-create-device-identity](includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure データ エクスプローラーでターゲット テーブルを作成する

次に、IoT Hub からデータを送信するテーブルを Azure Data Explorer に作成します。 「[**前提条件**](#prerequisites)」でプロビジョニングしたクラスターとデータベースにテーブルを作成します。

1. Azure portal でクラスターに移動し、**[クエリ]** を選択します。

    ![ポータルでの ADX クエリ](media/ingest-data-iot-hub/adx-initiate-query.png)

1. 次のコマンドをウィンドウにコピーし、 **[実行]** を選択して、取り込んだデータを受け取るテーブル (TestTable) を作成します。

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![クエリの作成の実行](media/ingest-data-iot-hub/run-create-query.png)

1. 次のコマンドをウィンドウにコピーし、 **[実行]** を選択して、テーブル (TestTable) の列名とデータ型に受信 JSON データをマップします。

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Azure Data Explorer テーブルを IoT ハブに接続する

ここで、Azure Data Explorer から IoT Hub に接続します。 この接続が完了すると、IoT ハブに送信されるデータは、[作成したターゲット テーブル](#create-a-target-table-in-azure-data-explorer)にストリーム配信されます。

1. ツールバーの **[通知]** を選択して、IoT Hub のデプロイが成功したことを確認します。

1. 作成したクラスターで、**[データベース]** を選択し、**testdb** を作成したデータベースを選択します。
    
    ![テスト データベースの選択](media/ingest-data-iot-hub/select-database.png)

1. **[データ インジェスト]** 、 **[データ接続の追加]** の順に選択します。

    :::image type="content" source="media/ingest-data-iot-hub/iot-hub-connection.png" alt-text="IoT Hub へのデータ接続の作成 - Azure Data Explorer":::

### <a name="create-a-data-connection"></a>データ接続を作成する

1. フォームに次の情報を入力します。 
    
    :::image type="content" source="media/ingest-data-iot-hub/data-connection-pane.png" alt-text="IoT Hub のデータ接続ペイン - Azure Data Explorer":::

    **設定** | **フィールドの説明**
    |---|---|
    | データ接続名 | Azure Data Explorer で作成する接続の名前
    | サブスクリプション |  イベントハブ リソースが配置されているサブスクリプション ID。  |
    | IoT Hub | IoT Hub 名 |
    | 共有アクセス ポリシー | 共有アクセス ポリシーの名前。 読み取りアクセス許可が必要 |
    | コンシューマー グループ |  IoT Hub の組み込みのエンドポイントに定義されているコンシューマー グループ |
    | イベント システム プロパティ | [IoT Hub イベントのシステム プロパティ](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages)。 システム プロパティを追加する場合は、テーブル スキーマと[マッピング](kusto/management/mappings.md)を[作成](kusto/management/create-table-command.md)または[更新](kusto/management/alter-table-command.md)して、選択したプロパティを含めます。 | | | 

#### <a name="target-table"></a>ターゲット テーブル

挿入したデータをルーティングするには、"*静的*" と "*動的*" という 2 つのオプションがあります。 この記事では、静的ルーティングを使用し、テーブル名、データ形式、およびマッピングを指定します。 イベントハブ メッセージにデータ ルーティング情報が含まれている場合、このルーティング情報によって既定の設定が上書きされます。

1. 次のルーティング設定を入力します。
    
    :::image type="content" source="media/ingest-data-iot-hub/default-routing-settings.png" alt-text="既定のルーティング プロパティ - IoT Hub - Azure Data Explorer":::

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | テーブル名 | *TestTable* | **testdb** に作成したテーブル。 |
    | データ形式 | *JSON* | サポートされている形式は、Avro、CSV、JSON、MULTILINE JSON、ORC、PARQUET、PSV、SCSV、SOHSV、TSV、TXT、TSVE、APACHEAVRO、および W3CLOG です。|
    | マッピング | *TestMapping* | **testdb** に作成した[マッピング](kusto/management/mappings.md)。これにより、受信データを **testdb** の列名とデータ型にマッピングします。 JSON、MULTILINE JSON、AVRO では必須。その他の形式では省略可能。|
    | | |

    > [!WARNING]
    > [手動フェールオーバー](/azure/iot-hub/iot-hub-ha-dr#manual-failover)が発生した場合は、データ接続を再作成する必要があります。
    
    > [!NOTE]
    > * **既定のルーティング設定**をすべて指定する必要はありません。 部分的な設定も受け入れられます。
    > * データ接続の作成後にエンキューされたイベントのみが取り込まれたます。

1. **［作成］** を選択します

### <a name="event-system-properties-mapping"></a>イベント システム プロパティのマッピング

> [!Note]
> * システム プロパティは、単一レコードのイベントに対してサポートされています。
> * `csv` マッピングの場合、レコードの先頭にプロパティが追加されます。 `json` マッピングの場合、ドロップダウン リストに表示される名前に従ってプロパティが追加されます。

テーブルの **[データ ソース]** セクションで **[イベント システムのプロパティ]** を選択した場合は、テーブル スキーマとマッピングに[システム プロパティ](ingest-data-iot-hub-overview.md#system-properties)を含める必要があります。

## <a name="generate-sample-data-for-testing"></a>テスト用のサンプル データを生成する

シミュレートされたデバイス アプリケーションは、IoT Hub 上のデバイスに固有のエンドポイントに接続し、シミュレートされた温度と湿度のテレメトリを送信します。

1. [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) からサンプル C# プロジェクトをダウンロードし、ZIP アーカイブを抽出します。

1. ローカル ターミナル ウィンドウで、サンプルの C# プロジェクトのルート フォルダーに移動します。 **iot-hub\Quickstarts\simulated-device** フォルダーに移動します。

1. 適当なテキスト エディターで **SimulatedDevice.cs** ファイルを開きます。

    `s_connectionString` 変数の値を、「[IoT Hub にデバイスを登録する](#register-a-device-to-the-iot-hub)」のデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.cs** ファイルに保存します。

1. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションに必要なパッケージをインストールします。

    ```cmd/sh
    dotnet restore
    ```

1. ローカル ターミナル ウィンドウで次のコマンドを実行し、シミュレートされたデバイス アプリケーションをビルドして実行します。

    ```cmd/sh
    dotnet run
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT Hub にテレメトリを送信したときの出力を示しています。

    ![シミュレートされたデバイスを実行する](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>データ フローの確認

データを生成するアプリで、IoT ハブからお使いのクラスター内のテーブルにそのデータが送信されるのを確認できるようになりました。

1. アプリの実行中に Azure portal でお使いの IoT ハブを確認すると、アクティビティの急上昇が見られます。

    ![IoT Hub メトリック](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. これまでにデータベースに届いたメッセージ数を確認するには、テスト データベースで次のクエリを実行します。

    ```Kusto
    TestTable
    | count
    ```

1. メッセージの内容を表示するには、次のクエリを実行します。

    ```Kusto
    TestTable
    ```

    結果セット:
    
    ![取り込まれたデータの結果の表示](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer には、インジェスト プロセスを最適化することを目的とした、データ インジェストの集計 (バッチ処理) ポリシーがあります。 既定では、このポリシーは 5 分または 500 MB のデータに構成されているため、待ち時間が生じることがあります。 集計オプションについては、[バッチ処理のポリシー](kusto/management/batchingpolicy.md)に関するページを参照してください。 
    > * ストリーミングをサポートし、応答時間でのラグを削除するようにテーブルを構成します。 [ストリーミング ポリシー](kusto/management/streamingingestionpolicy.md)に関するページを参照してください。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

現在の IoT Hub を今後使用する予定がない場合は、リソース グループをクリーンアップします。

1. Azure Portal の左端で **[リソース グループ]** を選択し、作成したリソース グループを選択します。  

    左側のメニューが折りたたまれている場合は、 ![展開ボタン](media/ingest-data-event-hub/expand.png) をクリックして展開します。

   ![削除するリソース グループの選択](media/ingest-data-iot-hub/delete-resources-select.png)

1. **test-resource-group** で **[リソース グループの削除]** を選択します。

1. 新しいのウィンドウで、削除するリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md)
