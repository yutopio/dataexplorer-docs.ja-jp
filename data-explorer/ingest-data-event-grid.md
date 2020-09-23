---
title: Azure Data Explorer に Azure BLOB を取り込む
description: この記事では、Event Grid サブスクリプションを使用して Azure Data Explorer にストレージ アカウントのデータを送信する方法を説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 5cab29b20ad726c1482fa892ad4dadece464f01d
ms.sourcegitcommit: 97404e9ed4a28cd497d2acbde07d00149836d026
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832725"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Event Grid の通知をサブスクライブすることで Azure Data Explorer に BLOB を取り込む

> [!div class="op_single_selector"]
> * [ポータル](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager テンプレート](data-connection-event-grid-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

この記事では、Event Grid データ接続を使用して、ストレージ アカウントから Azure Data Explorer に BLOB を取り込む方法を説明します。 [Azure Event Grid](/azure/event-grid/overview) サブスクリプションを設定する Event Grid データ接続を作成します。 Event Grid サブスクリプションは、Azure Event Hub 経由でストレージアカウントから Azure Data Explorer にイベントをルーティングします。 その後、システム全体のデータ フローの例が表示されます。

Event Grid から Azure Data Explorer への取り込みに関する一般的な情報については、[Event Grid への接続](ingest-data-event-grid-overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [無料の Azure アカウント](https://azure.microsoft.com/free/)を作成します。
* [クラスターとデータベース](create-cluster-database-portal.md)。
* [ストレージ アカウント](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)。

## <a name="create-a-target-table-in-azure-data-explorer"></a>Azure データ エクスプローラーでターゲット テーブルを作成する

Azure Data Explorer で、Event Hubs のデータの送信先となるテーブルを作成します。 前提条件で準備したクラスターとデータベース内にテーブルを作成します。

1. Azure portal のクラスターで、 **[クエリ]** を選択します。

    :::image type="content" source="media/ingest-data-event-grid/query-explorer-link.png" alt-text="クエリ エクスプローラーへのリンク"::: 

1. 次のコマンドをウィンドウにコピーし、 **[実行]** を選択して、取り込んだデータを受け取るテーブル (TestTable) を作成します。

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    :::image type="content" source="media/ingest-data-event-grid/run-create-table.png" alt-text="コマンド create table の実行":::

1. 次のコマンドをウィンドウにコピーし、 **[実行]** を選択して、テーブル (TestTable) の列名とデータ型に受信 JSON データをマップします。

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Azure Data Explorer でイベント グリッド データ接続を作成する

ここで、ストレージ アカウントを Azure Data Explorer に接続して、ストレージに送信されるデータがテスト テーブルにストリーミングされるようにします。 

1. 作成したクラスターの **[データベース]**  >  **[TestDatabase]** を選択します。

    :::image type="content" source="media/ingest-data-event-grid/select-test-database.png" alt-text="テスト データベースの選択":::

1. **[データ インジェスト]**  >  **[データ接続の追加]** を選択します。

    :::image type="content" source="media/ingest-data-event-grid/data-ingestion-create.png" alt-text="[データ インジェスト] の [データ接続の追加]":::

### <a name="data-connection---basics-tab"></a>データ接続 - [基本] タブ

1. 次の接続の種類を選択します: **[Blob ストレージ]** 。

1. フォームに次の情報を入力します。

    :::image type="content" source="media/ingest-data-event-grid/data-connection-basics.png" alt-text="Event Grid フォームへの接続の基本の入力":::

    |**設定** | **推奨値** | **フィールドの説明**|
    |---|---|---|
    | データ接続名 | *test-grid-connection* | Azure Data Explorer で作成する接続の名前。|
    | ストレージ アカウントのサブスクリプション | サブスクリプション ID | ストレージ アカウントが存在するサブスクリプション ID。|
    | ストレージ アカウント | *gridteststorage1* | 作成済みのストレージ アカウントの名前。|
    | イベントの種類 | "*作成された BLOB*" または "*名前変更された BLOB*" | インジェストをトリガーするイベントの種類。 |
    | リソースの作成 | *自動* | Azure Data Explorer で Event Grid サブスクリプション、イベント ハブの名前空間、およびイベント ハブを自動作成するかどうかを定義します。 Event Grid サブスクリプションを手動作成する方法については、「[お使いのストレージ アカウント内に Event Grid サブスクリプションを作成する](ingest-data-event-grid.md)」セクションの参照で詳しく説明しています。|

1. 特定のサブジェクトを追跡するには、 **[Filter settings]\(フィルターの設定\)** を選択します。 次のように、通知用のフィルターを設定します。
    * **[Prefix]\(プレフィックス\)** フィールドはサブジェクトの "*リテラル*" プレフィックスです。 適用されるパターンは *startswith* であるため、複数のコンテナー、フォルダー、BLOB を対象にできます。 ワイルドカードは使用できません。
        * BLOB コンテナーに対してフィルターを定義するには、フィールドを *`/blobServices/default/containers/[container prefix]`* のように設定する "*必要があります*"。
        * BLOB プレフィックス (または Azure Data Lake Gen2 のフォルダー) に対してフィルターを定義するには、フィールドを *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* のように設定する "*必要があります*"。
    * **[Suffix]\(サフィックス\)** フィールドは、BLOB の "*リテラル*" サフィックスです。 ワイルドカードは使用できません。
    * **[Case Sensitive]\(大文字と小文字の区別\)** フィールドは、プレフィックスとサフィックスのフィルターで大文字と小文字が区別されるかどうかを示します
    * イベントのフィルター処理の詳細については、[Blob Storage のイベント](/azure/storage/blobs/storage-blob-event-overview#filtering-events)に関するページを参照してください。
    
    :::image type="content" source="media/ingest-data-event-grid/filter-settings.png" alt-text="Event Grid のフィルター設定":::    

1. **[Next:Ingest properties]\(次へ: 取り込みのプロパティ\)** を選択します。

### <a name="data-connection---ingest-properties-tab"></a>データ接続 - [Ingest properties]\(取り込みのプロパティ\) タブ

1. フォームに次の情報を入力します。 テーブル名とマッピング名では大文字と小文字が区別されます。

   :::image type="content" source="media/ingest-data-event-grid/data-connection-ingest-properties.png" alt-text="テーブルとマッピングの取り込みのプロパティの確認と作成":::

    Ingest properties (取り込みのプロパティ):

     **設定** | **推奨値** | **フィールドの説明**
    |---|---|---|
    | テーブル名 | *TestTable* | **TestDatabase** に作成したテーブル。 |
    | データ形式 | *JSON* | サポートされている形式は、Avro、CSV、JSON、MULTILINE JSON、ORC、PARQUET、PSV、SCSV、SOHSV、TSV、TXT、TSVE、APACHEAVRO、RAW、および W3CLOG です。 サポートされている圧縮オプションは、Zip および GZip です。 |
    | マッピング | *TestMapping* | **TestDatabase** に作成したマッピング。受信 JSON データを **TestTable** の列名とデータ型にマッピングします。|
    | 詳細設定 | *データにヘッダーが含まれている* | ヘッダーを無視します。 *SV 型ファイルでサポートされています。|

   > [!NOTE]
   > **既定のルーティング設定**をすべて指定する必要はありません。 部分的な設定も受け入れられます。
1. **[Next:確認と作成]** を選択します

### <a name="data-connection---review--create-tab"></a>データ接続 - [確認と作成] タブ

1. 自動的に作成されたリソースを確認し、 **[作成]** を選択します。

    :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="Event Grid のデータ接続の確認と作成":::

### <a name="deployment"></a>デプロイ

デプロイが完了するまでお待ちください。 デプロイが失敗した場合は、失敗したステージの横にある **[操作の詳細]** を選択して、失敗の原因に関する詳細情報を取得します。 リソースのデプロイを再試行するには、 **[再デプロイ]** を選択します。 デプロイの前にパラメーターを変更できます。

:::image type="content" source="media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="Event Grid リソースのデプロイ":::

## <a name="generate-sample-data"></a>サンプル データを作成する

Azure Data Explorer とストレージ アカウントが接続されたので、サンプル データを作成してストレージ コンテナーにアップロードできます。

Azure Storage リソースを操作するいくつかの基本的な Azure CLI コマンドを発行する、簡単なシェル スクリプトを使用します。 このスクリプトでは、次のアクションを実行します。 
1. ストレージ アカウントに新しいコンテナーを作成する。
1. 既存のファイルを (BLOB として) そのコンテナーにアップロードする。
1. コンテナー内の BLOB を一覧表示する。 

[Azure Cloud Shell](/azure/cloud-shell/overview) を使用して、このスクリプトをポータルで直接実行できます。

ファイルにデータを保存し、このスクリプトでそれをアップロードします。

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> インジェストのパフォーマンスを最高にするには、インジェストのために送信される圧縮された BLOB の "*圧縮されていない状態*" でのサイズを伝える必要があります。 Event Grid の通知には基本情報しか含まれていないため、サイズ情報は明示的に伝達する必要があります。 圧縮されていないサイズの情報は、BLOB メタデータの `rawSizeBytes` プロパティに "*圧縮されていない*" データ サイズ (バイト単位) を設定することで提供できます。

### <a name="ingestion-properties"></a>インジェストのプロパティ

BLOB メタデータを使用して、BLOB インジェストの[インジェストのプロパティ](ingest-data-event-grid-overview.md#set-ingestion-properties)を指定できます。 

> [!NOTE]
> Azure Data Explorer では、BLOB 投稿の取り込みは削除されません。
> 3 から 5 日間、BLOB が保持されます。
> [Azure BLOB ストレージのライフサイクル](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)を使用して、BLOB の削除を管理してください。 

## <a name="review-the-data-flow"></a>データ フローの確認

> [!NOTE]
> Azure Data Explorer には、インジェスト プロセスを最適化することを目的とした、データ インジェストの集計 (バッチ処理) ポリシーがあります。
既定では、ポリシーは 5 分間に構成されます。
このポリシーは、必要に応じて、後で変更できます。 この記事では、数分間の待機時間が発生する可能性があります。

1. アプリの実行中に Azure portal でイベント グリッドを確認すると、アクティビティの急上昇が見られます。

    :::image type="content" source="media/ingest-data-event-grid/event-grid-graph.png" alt-text="Event Grid のアクティビティ グラフ":::

1. これまでにデータベースに届いたメッセージ数を確認するには、テスト データベースで次のクエリを実行します。

    ```kusto
    TestTable
    | count
    ```

1. メッセージの内容を表示するには、テスト データベースに対して次のクエリを実行します。

    ```kusto
    TestTable
    ```

    結果セットは次の図のようになります。

    :::image type="content" source="media/ingest-data-event-grid/table-result.png" alt-text="Event Grid のメッセージの結果セット":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Event Grid を再度使用する予定がない場合は、自動的に作成された Event Grid サブスクリプション、イベント ハブの名前空間、およびイベント ハブをクリーンアップしてコストが発生しないようにします。

1. Azure portal で、左側のメニューに移動して **[すべてのリソース]** を選択します。

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="Event Grid のクリーンアップ用にすべてのリソースを選択":::    

1. イベント ハブの名前空間を検索し、 **[削除]** を選択して削除します。

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-find-eventhub-namespace-delete.png" alt-text="イベント ハブの名前空間のクリーンアップ":::

1. [リソースの削除] フォームで、削除を確認して、イベント ハブの名前空間とイベント ハブのリソースを削除します。

1. ストレージ アカウントに移動します。 左側のメニューで **[イベント]** を選択します

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="Event Grid でクリーンアップするイベントの選択":::

1. グラフの下で Event Grid サブスクリプションを選択し、 **[削除]** を選択して削除します。

    :::image type="content" source="media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="Event Grid サブスクリプションの削除":::

1. Event Grid データ接続を削除するには、Azure Data Explorer クラスターにアクセスします。 メニューで **[データべース]** を選択します。

1. データベース **TestDatabase** を選択します。

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="リソースをクリーンアップするデータベースの選択":::

1. メニューで **[データ インジェスト]** を選択します。

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="[データ インジェスト] を選択してリソースをクリーンアップ":::

1. データ接続 *test-grid-connection* を選択し、 **[削除]** を選んで削除します。

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md)
