---
title: ワンクリックでのインジェストを使用して Azure Data Explorer にデータを取り込む
description: ワンクリックでのインジェストを使用するだけで、Azure Data Explorer にデータを取り込む (読み込む) 方法の概要を説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/29/2020
ms.openlocfilehash: 5ebd3ee711baa24c6f73facfdab361e2de5ada20
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528150"
---
# <a name="what-is-one-click-ingestion"></a>ワンクリックでのインジェストとは

ワンクリックでのインジェストを使用すると、データ インジェスト プロセスを簡単かつ迅速に、直感的に行うことができます。 ワンクリックでのインジェストは、データの取り込み、データベース テーブルの作成、マッピング構造の準備をすばやく行い、開始するのに役立ちます。 1 回限りまたは継続的なインジェスト プロセスとして、データ形式の異なるさまざまな種類のソースからデータを選択します。

次の機能により、ワンクリックでのインジェストが使いやすくなります。

* インジェスト ウィザードによる直感的なエクスペリエンス
* わずか数分でのデータの取り込み
* ローカル ファイル、BLOB、コンテナーなどの、さまざまな種類のソースからのデータの取り込み (最大 10,000 個の BLOB)
* さまざまな[形式](#file-formats)のデータの取り込み
* 新規または既存のテーブルへのデータの取り込み
* テーブル マッピングとスキーマが推奨されており、簡単に変更できる
* [Event Grid](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container) でコンテナーから簡単かつ迅速にインジェストを続ける

データの初回取り込み時やデータのスキーマに不慣れな場合に、ワンクリックでのインジェストは特に効果的です。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [Azure Data Explorer クラスターとデータベース](create-cluster-database-portal.md)を作成します。
* [Azure Data Explorer の Web UI にサインイン](https://dataexplorer.azure.com/)して、[クラスターへの接続を追加](web-query-data.md#add-clusters)します。

## <a name="access-the-one-click-wizard"></a>ワンクリック ウィザードにアクセスする

ワンクリックでのインジェスト ウィザードを使用すると、ワンクリックでのデータ取り込み手順を画面の指示に従って行うことができます。

* [Azure Data Explorer の Web UI](https://dataexplorer.azure.com/) からウィザードにアクセスするには、次のいずれかの方法を使用します。
    * 左側のペインで、 **[データ]** を選択します。 **[データ管理]** ページで、インジェストの種類を選択し、 **[取り込む]** をクリックします。 
      
      :::image type="content" source="media/ingest-data-one-click/data-management.png" alt-text="Web UI インターフェイスの [データ管理] ウィンドウからデータを取り込む - Azure Data Explorer" lightbox="media/ingest-data-one-click/data-management.png":::
   
     * Azure Data Explorer の Web UI の左側のメニューで **データベース** または **テーブル** の行を右クリックし、 **[Ingest new data]\(新しいデータの取り込み\)** を選択します。
        
        :::image type="content" source="media/ingest-data-one-click/one-click-ingestion-in-webui.png" alt-text="Web UI 上でのワンクリックでのインジェストの選択":::

* クラスターの **[Welcome to Azure Data Explorer]\(Azure Data Explorer へようこそ\)** ホーム画面からワンクリックでのインジェスト ウィザードにアクセスするには、最初の 2 つの手順 ([クラスターの作成とデータベースの作成](#prerequisites)) を完了してから、 **[新しいデータの取り込み]** を選択します。

    :::image type="content" source="media/ingest-data-one-click/welcome-ingestion.png" alt-text="[Welcome to Azure Data Explorer]\(Azure Data Explorer へようこそ\) からの新しいデータの取り込み":::


* Azure portal からウィザードにアクセスするには、左側のメニューから **[クエリ]** を選択し、**データベース** または **テーブル** を右クリックして、 **[Ingest new data]\(新しいデータの取り込み\)** を選択します。

    :::image type="content" source="media/ingest-data-one-click/access-from-portal.png" alt-text="Azure portal からワンクリックでのインジェスト ウィザードにアクセスする":::

## <a name="one-click-ingestion-wizard"></a>ワンクリックでのインジェスト ウィザード

> [!NOTE]
> ここでは、ウィザード全般について説明します。 選択するオプションは、取り込むデータの形式、取り込み元のデータ ソースの種類、新しいテーブルまたは既存のテーブルのどちらに取り込むかによって異なります。
>
> サンプル シナリオについては、以下をご覧ください。
> * [CSV 形式でコンテナーから新しいテーブル](one-click-ingestion-new-table.md)に取り込む
> * [JSON 形式でローカル ファイルから既存のテーブル](one-click-ingestion-existing-table.md)に取り込む 

ウィザードの指示に従って、次のオプションを選択します。
   * [既存のテーブル](one-click-ingestion-existing-table.md)への取り込み
   * [新しいテーブル](one-click-ingestion-new-table.md)への取り込み
   * 次の場所からのデータの取り込み:
      * BLOB ストレージ: 最大 10 個の BLOB
      * [ローカル ファイル](one-click-ingestion-existing-table.md): 最大 10 個のファイル
      * [コンテナー](one-click-ingestion-new-table.md) (BLOB コンテナー、ADLS Gen1 コンテナー、ADLS Gen2 コンテナー)

### <a name="schema-mapping"></a>スキーマ マッピング

サービスでは、変更可能なスキーマおよびインジェストのプロパティが自動的に生成されます。 新しいテーブルまたは既存のテーブルのどちらに取り込むかによって、既存のマッピング構造を使用することも、新しいマッピング構造を作成することもできます。

**[スキーマ]** タブで、次の操作を行います。
   * 自動生成された圧縮の種類を確認します。
   * [データの形式](#file-formats)を選択します。 異なる形式を使用すると、さらに変更を加えることができます。
   * [[エディター] ウィンドウ](#editor-window)でマッピングを変更します。

#### <a name="file-formats"></a>ファイル形式

ワンクリックでのインジェストでは、[インジェスト用に Azure Data Explorer でサポートされているすべてのデータ形式](ingestion-supported-formats.md)からデータを取り込むことができます。

### <a name="editor-window"></a>エディター ウィンドウ

**[スキーマ]** タブの **[エディター]** ウィンドウで、必要に応じてデータ テーブルの列を調整できます。 

[!INCLUDE [data-explorer-one-click-column-table](includes/data-explorer-one-click-column-table.md)]

>[!NOTE]
> **[エディター]** ペインの上にある [コマンド エディター](one-click-ingestion-new-table.md#command-editor)をいつでも開くことができます。 コマンド エディターでは、ご自分の入力から生成された自動コマンドを表示およびコピーできます。

#### <a name="mapping-transformations"></a>マッピング変換

一部のデータ形式マッピング (Parquet、JSON、Avro) では、簡単な取り込み時の変換がサポートされています。 マッピング変換を適用するには、[エディター ウィンドウ](#editor-window)で列を作成または更新します。

マッピング変換は、データ型が int または long である **ソース** を使用して、string または datetime **型** の列に対して実行できます。 サポートされているマッピング変換は次のとおりです。
* DateTimeFromUnixSeconds
* DateTimeFromUnixMilliseconds
* DateTimeFromUnixMicroseconds
* DateTimeFromUnixNanoseconds

詳細については、[マッピング変換に関するページ](#mapping-transformations)を参照してください。

### <a name="data-ingestion"></a>データ インジェスト

スキーマ マッピングと列の操作が完了すると、インジェスト ウィザードでデータ インジェスト プロセスが開始されます。 

* **コンテナー以外** のソースからのデータ取り込みは、直ちに結果が反映されます。

* データ ソースが **コンテナー** の場合は、次のようになります。
    * Azure Data Explorer の[バッチ処理ポリシー](kusto/management/batchingpolicy.md)によりデータが集計されます。 
    * インジェストが完了すると、インジェスト レポートをダウンロードして、処理された各 BLOB のパフォーマンスを確認できます。 
    * **[Create continuous ingestion]\(継続的なインジェストの作成\)** を選択し、[Event Grid を使用して継続的なインジェスト](one-click-ingestion-new-table.md#create-continuous-ingestion-for-container)を設定することができます。
 
### <a name="initial-data-exploration"></a>初期データ探索
   
インジェストが完了すると、ウィザードには、データの初期探索で **[クイック コマンド](one-click-ingestion-existing-table.md#explore-quick-queries-and-tools)** を使用するためのオプションが表示されます。


## <a name="next-steps"></a>次のステップ

* [ワンクリックでのインジェストを使用して Azure Data Explorer の既存のテーブルにローカル ファイルの JSON データを取り込む](one-click-ingestion-existing-table.md)
* [ワンクリックでのインジェストを使用して Azure Data Explorer の新しいテーブルにコンテナーの CSV データを取り込む](one-click-ingestion-new-table.md)
* [Azure Data Explorer の Web UI でデータのクエリを実行する](web-query-data.md)
* [Kusto クエリ言語を使用して Azure Data Explorer のクエリを作成する](write-queries.md)
