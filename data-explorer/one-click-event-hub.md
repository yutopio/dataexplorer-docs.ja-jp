---
title: ワンクリックでのインジェストを使用してイベント ハブから Azure Data Explorer にデータを取り込みます。
description: この記事では、ワンクリック エクスペリエンスを使用してイベント ハブから Azure Data Explorer にデータを取り込む (読み込む) 方法について学習します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 983b9c59e6130c58d04e1d4f1a7042a3aa73bba7
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554936"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-data-explorer"></a>ワンクリックでのインジェストを使用して、Azure Data Explorer 用にイベント ハブ データ接続を作成する

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-event-hub.md)
> * [ワンクリック](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager テンプレート](data-connection-event-hub-resource-manager.md)

Azure データ エクスプローラーには、Event Hubs からの取り込み (データの読み込み)、ビッグ データのストリーミング プラットフォーム、イベント取り込みサービスの機能があります。 [Event Hubs](/azure/event-hubs/event-hubs-about) は、1 秒あたり数百万件のイベントをほぼリアルタイムで処理できます。 この記事では、[ワンクリックでのインジェスト](ingest-data-one-click.md) エクスペリエンスを使用して、Azure Data Explorer のテーブルにイベント ハブを接続します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [クラスターとデータベース](create-cluster-database-portal.md)。
* [取り込み用のデータが含まれるイベント ハブ](ingest-data-event-hub.md#create-an-event-hub)。

## <a name="ingest-new-data"></a>新しいデータを取り込む

1. [Web UI](https://dataexplorer.azure.com/) の左側のメニューで、"*データベース*" または "*テーブル*" を右クリックし、 **[Ingest new data]\(新しいデータの取り込み\)** を選択します。 

:::image type="content" source="media/one-click-event-hub/one-click-ingestion-in-web-ui.png" alt-text="Web UI 上でのワンクリックでのインジェストの選択":::

**[Ingest new data]\(新しいデータの取り込み\)** ウィンドウが、 **[ソース]** タブが選択された状態で開きます。

:::image type="content" source="media/one-click-event-hub/reference-to-event-hub.png" alt-text="ソース = イベント ハブへの参照である、Azure Data Explorer への新しいデータの取り込みの [ソース] タブのスクリーンショット":::

1. **[データベース]** フィールドには、お使いのデータベースが自動的に設定されます。 ドロップダウン メニューから別のデータベースを選択できます。

1. **[テーブル]** で、 **[新規作成]** を選択して新しいテーブルの名前を入力するか、既存のテーブルを使用します。 

    > [!NOTE]
    > テーブル名は、1 から 1024 文字にする必要があります。 英数字、ハイフン、アンダースコアを使用できます。 特殊文字はサポートされていません。

1. **[ソースの種類]** で、 **[Reference to Event Hub]\(イベント ハブへの参照\)** を選択します。 データ接続の選択肢が表示されます。

## <a name="data-connection"></a>データ接続

1. **[データ接続]** で、次のフィールドに入力します。

    :::image type="content" source="media/one-click-event-hub/project-details.png" alt-text="プロジェクト詳細のフィールドが入力された [ソース] タブのスクリーンショット - ワンクリック エクスペリエンスでのイベント ハブを使用した Azure Data Explorer への新しいデータの取り込み":::

    |**設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | データ接続名 | *TestDataConnection*  | データ接続を識別する名前。
    | サブスクリプション |      | イベントハブ リソースが配置されているサブスクリプション ID。  |
    | Event Hub 名前空間 |  | 名前空間を識別する名前。 |
    | イベント ハブ |  | 使用するイベント ハブ。 |
    | コンシューマー グループ |  | イベント ハブに定義されているコンシューマー グループ。 |
    | データ形式 | | データは [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) オブジェクトの形式でイベント ハブから読み取られます。 サポートされている形式は、CSV、JSON、PSV、SCsv、SOHsv、TSV、TSVE です。 |
    | イベント システム プロパティ | 関連するプロパティを選択する | [イベント ハブのシステム プロパティ](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations)。 1 つのイベント メッセージに複数のレコードがある場合、システム プロパティは最初のものに追加されます。 システム プロパティを追加する場合は、テーブル スキーマと[マッピング](kusto/management/mappings.md)を[作成](kusto/management/create-table-command.md)または[更新](kusto/management/alter-table-command.md)して、選択したプロパティを含めます。 |

1. **[スキーマの編集]** を選択します。

## <a name="schema-tab"></a>スキーマ タブ

JSON 形式のデータを使用したスキーマ マッピングの情報については、「[スキーマを編集する](one-click-ingestion-existing-table.md#edit-the-schema)」を参照してください。
CSV 形式のデータを使用したスキーマ マッピングの情報については、「[スキーマを編集する](one-click-ingestion-new-table.md#edit-the-schema)」を参照してください。

:::image type="content" source="media/one-click-event-hub/schema-tab.png" alt-text="ワンクリック エクスペリエンスでのイベント ハブを使用した Azure Data Explorer への新しいデータの取り込みの [スキーマ] タブのスクリーンショット":::

1. プレビュー ウィンドウに表示されるデータが完全でない場合は、必要なすべてのデータ フィールドを含むテーブルを作成するために、さらにデータが必要になることがあります。 次のコマンドを使用して、イベント ハブから新しいデータをフェッチします。
    * **Discard and fetch new data (破棄して新しいデータをフェッチする)** : 表示されたデータを破棄し、新しいイベントを検索します。
    * **Fetch more data (さらにデータをフェッチする)** :既に見つかったイベントに加えて、さらにイベントを検索します。 
    
    > [!NOTE]
    > データのプレビューを表示するには、イベント ハブがイベントを送信している必要があります。
        
1. **[Start ingestion]\(インジェストの開始\)** を選択します。

## <a name="complete-data-ingestion"></a>データ インジェストを完了する

**[Data ingestion completed]\(データ インジェストが完了\)** ウィンドウでは、データ インジェストが正常に終了した場合、ステップすべてに緑色のチェックマークが表示されます。 これらの手順の下にあるカードでは、 **[Quick queries]\(クイック クエリ\)** を使用してデータを探索したり、 **[ツール]** を使用して行った変更を元に戻したり、イベント ハブの接続とデータを **監視** したりするためのオプションが提供されます。

:::image type="content" source="media/one-click-event-hub/data-ingestion-completed.png" alt-text="ワンクリック エクスペリエンスを使用したイベント ハブからの Azure Data Explorer へのインジェストにおける最後の画面のスクリーンショット":::

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer の Web UI でデータのクエリを実行する](web-query-data.md)
* [Kusto クエリ言語を使用して Azure Data Explorer のクエリを作成する](write-queries.md)
