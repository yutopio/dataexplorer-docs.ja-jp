---
title: Azure Data Explorer でテーブルを作成する
description: Azure Data Explorer でワンクリックで簡単にテーブルを作成する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/06/2020
ms.openlocfilehash: 8732edf3b2cb9600fcce1ed03097d893c6615528
ms.sourcegitcommit: c2ab3176db4dd55ac9ca8eee52bbd24096d1277f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2020
ms.locfileid: "90740907"
---
# <a name="create-a-table-in-azure-data-explorer-preview"></a>Azure Data Explorer でテーブルを作成する (プレビュー)

テーブルの作成は、Azure Data Explorer の[データ インジェスト](ingest-data-overview.md)と[クエリ](write-queries.md)のプロセスにおける重要な手順です。 Azure Data Explorer で[クラスターとデータベースを作成](create-cluster-database-portal.md)した後、テーブルを作成することができます。 次の記事では、Azure Data Explorer Web UI を使用して、テーブルとスキーマのマッピングをすばやく簡単に作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成します。
* [Azure Data Explorer の Web UI にサインイン](https://dataexplorer.azure.com/)して、[クラスターへの接続を追加](web-query-data.md#add-clusters)します。

## <a name="create-a-table"></a>テーブルを作成する

1. Web UI の左側のメニューで、データベース名として **[ExampleDB]** を右クリックし、 **[Create table (preview)]\(テーブルの作成 (プレビュー\))** を選択します。

    :::image type="content" source="./media/one-click-table/create-table.png" alt-text="Azure Data Explorer Web UI でテーブルを作成する":::

**[ソース]** タブが選択された状態で **[テーブルの作成]** ウィンドウが開きます。
1. **[データベース]** フィールドには、お使いのデータベースが自動的に設定されます。 ドロップダウン メニューから別のデータベースを選択できます。
1. **[テーブル名]** に、お使いのテーブルの名前を入力します。 
    > [!TIP]
    >  テーブル名には、英数字、ハイフン、およびアンダースコアを含む最大 1024 文字を使用できます。 特殊文字はサポートされていません。

### <a name="select-source-type"></a>ソースの種類を選択する

1. **[ソースの種類]** で、テーブル マッピングの作成に使用するデータ ソースを選択します。 次のオプションから選択します。 **[BLOB から]** 、 **[ファイルから]** 、または **[コンテナーから]** 。
   
    
    * **BLOB** を使用している場合:
        * BLOB のストレージ URL を入力し、必要に応じてサンプル サイズを入力します。 
        * **[ファイルのフィルター]** を使用してファイルをフィルター処理します。 
        * 次の手順でスキーマの定義に使用するファイルを選択します。

        :::image type="content" source="media/one-click-table/blob.png" alt-text="BLOB を使用してテーブルを作成し、スキーマ マッピングを作成する":::
    
    * **ローカル ファイル**を使用している場合:
        * **[参照]** を選択してファイルを見つけるか、ファイルをフィールドにドラッグします。

        :::image type="content" source="./media/one-click-table/data-from-file.png" alt-text="ローカル ファイルのデータに基づいてテーブルを作成する":::

    * **コンテナー**を使用している場合:
        * **[ストレージへのリンク]** フィールドにコンテナーの [SAS URL](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) を追加し、必要に応じてサンプル サイズを入力します。 

1. **[スキーマの編集]** を選択し、 **[スキーマ]** タブに進みます。

### <a name="edit-schema"></a>スキーマを編集する

**[スキーマ]** タブでは、お使いの[データ形式](ingest-data-one-click.md#file-formats)と圧縮が左側のペインで自動的に識別されます。 正しく識別されない場合は、 **[データ形式]** ドロップダウン メニューを使用して、正しい形式を選択します。

   * データ形式が JSON の場合は、1 から 10 までの JSON レベルも選択する必要があります。 レベルによって、テーブル列のデータ区分が異なります。
   * データ形式が CSV の場合、ファイルの見出し行を無視するには、 **[Include column names]\(列名を含める\)** チェック ボックスをオンにします。

        :::image type="content" source="./media/one-click-table/schema-tab.png" alt-text="Azure Data Explorer のワンクリック エクスペリエンスでのテーブル作成における [スキーマの編集] タブ":::
 
1. **[マッピング]** で、このテーブルのスキーマ マッピングの名前を入力します。 
    > [!TIP]
    >  テーブル名には、英数字とアンダースコアを含めることができます。 スペース、特殊文字、ハイフンはサポートされません。
1. **［作成］** を選択します

## <a name="create-table-completed-window"></a>[Create table completed]\(テーブルの作成が完了しました\) ウィンドウ

**[Create table completed]\(テーブルの作成が完了しました\)** ウィンドウでは、テーブルの作成が正常に終了した場合、両方のステップに緑色のチェックマークが表示されます。

* **[コマンドの表示]** を選択して、各ステップのエディターを開きます。 
    * エディターでは、ご自分の入力から生成された自動コマンドを表示およびコピーできます。
    
    :::image type="content" source="./media/one-click-table/table-completed.png" alt-text="Azure Data Explorer のワンクリック エクスペリエンスでのテーブル作成における [Create table completed]\(テーブルの作成が完了しました\)":::
 
**[テーブルの作成]** の進行状況の下にあるタイルで、 **[Quick queries]\(クイック クエリ\)** または **[ツール]** を探索します。

* **[Quick queries]\(クイック クエリ\)** には、クエリの例がある Web UI へのリンクが含まれています。

* **[ツール]** には、関連する .drop コマンドの実行の **[元に戻す]** へのリンクが含まれています。

> [!NOTE]
> .drop コマンドを使用すると、データが失われる可能性があります。<br>
> このワークフローのドロップ コマンドは、テーブル作成プロセスによって行われた変更 (新しいテーブルとスキーマのマッピング) を元に戻すだけです。

## <a name="next-steps"></a>次のステップ

* [データ取り込みの概要](ingest-data-overview.md)
* [ワンクリックでのインジェスト](ingest-data-one-click.md)
* [Azure Data Explorer のクエリを記述する](write-queries.md)  
