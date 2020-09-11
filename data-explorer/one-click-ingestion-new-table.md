---
title: ワンクリックでのインジェストを使用して Azure Data Explorer の新しいテーブルにコンテナーの CSV データを取り込む
description: ワンクリックでのインジェストを使用するだけで、新しい Azure Data Explorer テーブルにデータを取り込み (読み込み) ます。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 9427692d0533550967bfe84a35fd833a4c03b39e
ms.sourcegitcommit: 811cf98edefd919b412d80201400919eedcab5cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89274809"
---
# <a name="use-one-click-ingestion-to-ingest-csv-data-from-a-container-to-a-new-table-in-azure-data-explorer"></a>ワンクリックでのインジェストを使用して Azure Data Explorer の新しいテーブルにコンテナーの CSV データを取り込む

> [!div class="op_single_selector"]
> * [コンテナーから新しいテーブルに CSV データを取り込む](one-click-ingestion-new-table.md)
> * [ローカル ファイルから既存のテーブルに JSON データを取り込む](one-click-ingestion-existing-table.md)

[ワンクリックでのインジェスト](ingest-data-one-click.md)を使用すると、JSON、CSV、その他の形式のデータをすばやくテーブルに取り込んで、マッピング構造を簡単に作成することができます。 データは、ストレージ、ローカル ファイル、コンテナーから、1 回限りまたは継続的なインジェスト プロセスとして取り込むことができます。  

このドキュメントでは、特定のユース ケースで直感的なワンクリック ウィザードを使用して、**コンテナー**の **CSV** データを**新しいテーブル**に取り込む方法について説明します。 この同じプロセスにわずかな調整を行えば、さまざまなユース ケースに対応できます。

ワンクリックでのインジェストの概要と一連の前提条件については、[ワンクリックでのインジェスト](ingest-data-one-click.md)に関するページを参照してください。
Azure Data Explorer の既存のテーブルにデータを取り込む方法については、[既存のテーブルへのワンクリックでのインジェスト](one-click-ingestion-existing-table.md)に関するページを参照してください。

## <a name="ingest-new-data"></a>新しいデータを取り込む

1. Web UI の左側のメニューで、"*データベース*" を右クリックし、 **[Ingest new data (Preview)]\(新しいデータの取り込み (プレビュー)\)** を選択します。

    :::image type="content" source="media/one-click-ingestion-new-table/one-click-ingestion-in-web-ui.png" alt-text="新しいデータの取り込み":::

1. **[Ingest new data (Preview)]\(新しいデータの取り込み (プレビュー)\)** ウィンドウの **[ソース]** タブが選択されます。 

1. **[新しいテーブルの作成]** を選択し、新しいテーブルの名前を入力します。 英数字、ハイフン、アンダースコアを使用できます。 特殊文字はサポートされていません。

    > [!NOTE]
    > テーブル名は、1 から 1024 文字にする必要があります。

    :::image type="content" source="media/one-click-ingestion-new-table/create-new-table.png" alt-text="ワンクリック インジェストによる新しいテーブルの作成":::

## <a name="select-an-ingestion-type"></a>インジェストの種類を選択する

**[インジェストの種類]** で、次の手順を実行します。
   
  1. **[コンテナーから]** を選択します。 
  1. **[ストレージへのリンク]** フィールドにコンテナーの [SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) を追加し、必要に応じてサンプル サイズを入力します。

      :::image type="content" source="media/one-click-ingestion-new-table/from-container.png" alt-text="コンテナーからのワンクリックでのインジェスト":::

     > [!TIP] 
     > **ファイルから**のインジェストについては、[ワンクリックでのインジェストを使用した、Azure Data Explorer の既存のテーブルへのローカル ファイルの JSON データの取り込み](one-click-ingestion-existing-table.md#select-an-ingestion-type)に関するページを参照してください

データのサンプルが表示されます。 必要に応じて、データをフィルター処理して、特定の文字で始まる、または終わるファイルだけを取り込んでください。 フィルターを調整すると、プレビューが自動的に更新されます。

たとえば、フィルター処理を行って、 *.csv* 拡張子で開始するすべてのファイルを検出します。

:::image type="content" source="media/one-click-ingestion-new-table/from-container-with-filter.png" alt-text="ワンクリックでのインジェスト フィルター":::
  
## <a name="edit-the-schema"></a>スキーマを編集する

テーブル列の構成を表示および編集するには、 **[スキーマの編集]** を選択します。 このシステムでは、いずれか 1 つの BLOB がランダムに選択され、その BLOB に基づいてスキーマが生成されます。 ソースが圧縮されているかどうかは、その名前を見て自動的に識別されます。

**[スキーマ]** タブ内:

   1. **[データ形式]** を選択します。

        この場合、データ形式は **CSV** です

        > [!TIP]
        > **JSON** ファイルを使用する場合は、[ワンクリックでのインジェストを使用した、Azure Data Explorer の既存のテーブルへのローカル ファイルの JSON データの取り込み](one-click-ingestion-existing-table.md#edit-the-schema)に関するページを参照してください。

   1. ファイルの見出し行が無視されるように、 **[列名を含む]** チェック ボックスをオンにすることができます。

        :::image type="content" source="media/one-click-ingestion-new-table/non-json-format.png" alt-text="[列名を含む] をオンにする":::

**[マッピング名]** フィールドに、マッピング名を入力します。 英数字とアンダースコアを使用できます。 スペース、特殊文字、ハイフンはサポートされません。

:::image type="content" source="media/one-click-ingestion-new-table/table-mapping.png" alt-text="ワンクリックでのインジェストのテーブル マッピング名":::

### <a name="edit-the-table"></a>テーブルを編集する

新しいテーブルに取り込むときは、テーブルの作成時にテーブルのさまざまな側面を変更します。

この表で、 
 * 編集するには、新しい列の名前をダブルクリックします。
 * 新しい列ヘッダーを選択して、次のいずれかの操作を行います。

    [!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

  > [!NOTE]
  > 表形式データの列はそれぞれ Azure Data Explorer の 1 つの列に取り込むことができます。

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>インジェストを開始する

テーブルおよびマッピングを作成し、データ インジェストを開始するには、 **[Start ingestion]\(インジェストの開始\)** を選択します。

:::image type="content" source="media/one-click-ingestion-new-table/start-ingestion.png" alt-text="ワンクリックでのインジェストでのインジェストの開始":::

## <a name="complete-data-ingestion"></a>データ インジェストを完了する

**[データ インジェストが完了]** ウィンドウでは、データ インジェストが正常に終了した場合、3 つのステップすべてに緑色のチェックマークが表示されます。

:::image type="content" source="media/one-click-ingestion-new-table/one-click-data-ingestion-complete.png" alt-text="ワンクリックでのインジェストが完了"::: 

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="create-continuous-ingestion-for-container"></a>コンテナー用の継続的なインジェストを作成する

継続的なインジェストを使用すると、ソース コンテナー内の新しいファイルをリッスンするイベント グリッドを作成できます。 あらかじめ定義されたパラメーターの条件 (プレフィックス、サフィックスなど) を満たす新しいファイルがすべて自動的にターゲット テーブルに取り込まれます。 

1. **[継続的なインジェスト]** タイルで **[Event Grid]** を選択して、Azure portal を開きます。 イベント グリッドのデータ コネクタが開いた状態で [データ接続] ページが表示されます。ソース パラメーターとターゲット パラメーターは既に入力されています (ソース コンテナー、テーブル、マッピング)。
    
    :::image type="content" source="media/one-click-ingestion-new-table/continuous-button.png" alt-text="継続的なインジェストのボタン":::

1. **[作成]** を選択すると、そのコンテナー内の変更、更新、新しいデータをリッスンするデータ接続が作成されます。 

    :::image type="content" source="media/one-click-ingestion-new-table/event-hub-create.png" alt-text="イベント ハブ接続の作成":::

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer の Web UI でデータのクエリを実行する](web-query-data.md)
* [Kusto クエリ言語を使用して Azure Data Explorer のクエリを作成する](write-queries.md)
