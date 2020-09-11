---
title: ワンクリックでのインジェストを使用して Azure Data Explorer の既存のテーブルにローカル ファイルの JSON データを取り込む
description: ワンクリックでのインジェストを使用するだけで、既存の Azure Data Explorer テーブルにデータを取り込み (読み込み) ます。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 49195d39ed2c81a6f7dca685781cbe265e8843d3
ms.sourcegitcommit: 811cf98edefd919b412d80201400919eedcab5cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89274683"
---
# <a name="use-one-click-ingestion-to-ingest-json-data-from-a-local-file-to-an-existing-table-in-azure-data-explorer"></a>ワンクリックでのインジェストを使用して Azure Data Explorer の既存のテーブルにローカル ファイルの JSON データを取り込む


> [!div class="op_single_selector"]
> * [コンテナーから新しいテーブルに CSV データを取り込む](one-click-ingestion-new-table.md)
> * [ローカル ファイルから既存のテーブルに JSON データを取り込む](one-click-ingestion-existing-table.md)

[ワンクリックでのインジェスト](ingest-data-one-click.md)を使用すると、JSON、CSV、その他の形式のデータをすばやくテーブルに取り込んで、マッピング構造を簡単に作成することができます。 データは、ストレージ、ローカル ファイル、コンテナーから、1 回限りまたは継続的なインジェスト プロセスとして取り込むことができます。  

このドキュメントでは、特定のユース ケースで直感的なワンクリック ウィザードを使用して、**既存のテーブル**に**ローカル ファイル**の **JSON** データを取り込む方法について説明します。 この同じプロセスにわずかな調整を行えば、さまざまなユース ケースに対応できます。

ワンクリックでのインジェストの概要と一連の前提条件については、[ワンクリックでのインジェスト](ingest-data-one-click.md)に関するページを参照してください。
データのさまざまな種類またはソースについては「[ワンクリックでのインジェストを使用して Azure Data Explorer の新しいテーブルにコンテナーの CSV データを取り込む](one-click-ingestion-new-table.md)」を参照してください。

## <a name="ingest-new-data"></a>新しいデータを取り込む

Web UI の左側のメニューで、"*データベース*" または "*テーブル*" を右クリックし、 **[Ingest new data (Preview)]\(新しいデータの取り込み (プレビュー)\)** を選択します。

   :::image type="content" source="media/one-click-ingestion-existing-table/one-click-ingestion-in-webui.png" alt-text="Web UI 上でのワンクリックでのインジェストの選択":::
 
## <a name="select-an-ingestion-type"></a>インジェストの種類を選択する

1. **[Ingest new data (Preview)]\(新しいデータの取り込み (プレビュー)\)** ウィンドウの **[ソース]** タブが選択されます。

1. **[テーブル]** フィールドの内容が自動的に設定されない場合は、ドロップダウン メニューから既存のテーブル名を選択します。

    > [!NOTE]
    > *テーブル*の行で **[Ingest new data (Preview)] (新しいデータの取り込み (プレビュー))** を選択した場合は、選択したテーブル名が **[プロジェクトの詳細]** に表示されます。

1. **[インジェストの種類]** で、次の手順を実行します。

   1. **[ファイルから]** を選択します  
   1. **[参照]** を選択してファイルを見つけるか、ファイルをフィールドにドラッグします。
    
      :::image type="content" source="media/one-click-ingestion-existing-table/from-file.png" alt-text="ファイルからのワンクリックでのインジェスト":::

 1. データのサンプルが表示されます。 データをフィルター処理して、特定の文字で始まる、または終わるファイルだけを取り込みます。 
   
    >[!NOTE] 
    >フィルターを調整すると、プレビューが自動的に更新されます。
  
> [!TIP]
> **コンテナーから**のインジェストについては、[ワンクリックでのインジェストを使用した、Azure Data Explorer の新しいテーブルへのコンテナーの CSV データの取り込み](one-click-ingestion-new-table.md#select-an-ingestion-type)に関するページを参照してください

## <a name="edit-the-schema"></a>スキーマを編集する

テーブル列の構成を表示および編集するには、 **[スキーマの編集]** を選択します。

### <a name="map-columns"></a>列のマップ 

1. **[Map columns]\(列のマップ\)** ダイアログが開きます。 1 つ以上のソース列または属性を Azure Data Explorer の列にアタッチします。
    * 新しいマッピングが自動的に設定されます。または、既存のマッピングを使用します。 
    * **[Source columns]\(ソース列\)** の各フィールドに、 **[Target columns]\(ターゲット列\)** とマップする列名を入力します。
    * マッピングから列を削除するには、ごみ箱アイコンを選択します。

      :::image type="content" source="media/one-click-ingestion-existing-table/map-columns.png" alt-text="[列のマップ] ウィンドウ"::: 
    
1. **[Update]\(更新\)** を選択します。
1. **[スキーマ]** タブ内:
    * **[圧縮の種類]** は、ソース ファイルの名前に基づいて自動的に選択されます。 この場合、圧縮の種類は **JSON** です
        
    * **JSON** を選択する場合、1 から 10 の **[JSON レベル]** も選択する必要があります。 レベルによって、テーブル列のデータ区分が異なります。

        :::image type="content" source="media/one-click-ingestion-existing-table/json-levels.png" alt-text="JSON レベルの選択":::
    
       > [!TIP]
       > **CSV** ファイルを使用する場合、[ワンクリックでのインジェストを使用した、Azure Data Explorer の新しいテーブルへのコンテナーの CSV データの取り込み](one-click-ingestion-new-table.md#edit-the-schema)に関するページを参照してください

### <a name="edit-the-table"></a>テーブルを編集する 

既存のテーブルにデータを取り込む場合、テーブルに対して行うことができる変更は、より制限されます。

この表で、 
* 新しい列ヘッダーを選択して**新しい列**の追加、**列の削除**、**昇順での並べ替え**、**降順での並べ替え**を実行できます。 
* 既存の列で選択できるのは、データの並べ替えのみです。

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

[!INCLUDE [data-explorer-one-click-command-editor](includes/data-explorer-one-click-command-editor.md)]

## <a name="start-ingestion"></a>インジェストを開始する

テーブルおよびマッピングを作成し、データ インジェストを開始するには、 **[Start ingestion]\(インジェストの開始\)** を選択します。

:::image type="content" source="media/one-click-ingestion-existing-table/start-ingestion.png" alt-text="インジェストを開始する":::

## <a name="complete-data-ingestion"></a>データ インジェストを完了する

**[データ インジェストが完了]** ウィンドウでは、データ インジェストが正常に終了した場合、3 つのステップすべてに緑色のチェックマークが表示されます。

:::image type="content" source="media/one-click-ingestion-existing-table/one-click-data-ingestion-complete.png" alt-text="ワンクリックでのインジェストが完了":::

> [!IMPORTANT]
> コンテナーからの継続的なインジェストを設定するには、[ワンクリックでのインジェストを使用した、Azure Data Explorer の新しいテーブルへのコンテナーの CSV データの取り込み](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)に関するページを参照してください

[!INCLUDE [data-explorer-one-click-ingestion-query-data](includes/data-explorer-one-click-ingestion-query-data.md)]

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer の Web UI でデータのクエリを実行する](web-query-data.md)
* [Kusto クエリ言語を使用して Azure Data Explorer のクエリを作成する](write-queries.md)
