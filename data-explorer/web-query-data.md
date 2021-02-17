---
title: クイック スタート:Azure Data Explorer の Web UI でデータのクエリを実行する
description: このクイック スタートでは、Azure Data Explorer の Web UI でデータのクエリと共有を行う方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: quickstart
ms.date: 02/09/2021
ms.localizationpriority: high
ms.openlocfilehash: d581ade4a9cbba083e8cebf39a30f01586d2635c
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100360208"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>クイック スタート:Azure Data Explorer の Web UI でデータのクエリを実行する

Azure Data Explorer は、大量のデータのリアルタイム分析を実現するフル マネージドの高速データ分析サービスです。 Azure Data Explorer の Web エクスペリエンスを使用すると、Azure Data Explorer クラスターに接続して、Kusto クエリ言語のコマンドとクエリを作成、実行、共有できます。 Web エクスペリエンスは、Azure portal で利用でき、スタンドアロン Web アプリケーション ([Azure Data Explorer Web UI](https://dataexplorer.azure.com)) としても使用できます。 Azure Data Explorer Web UI は、他の Web ポータルの HTML iframe 内でホストすることもできます。 Web UI および使用される Monaco エディターをホストする方法の詳細については、[Monaco IDE の統合](kusto/api/monaco/monaco-kusto.md)に関するページを参照してください。
このクイックスタートでは、スタンドアロンの Azure Data Explorer Web UI を使用します。

:::image type="content" source="media/web-query-data/walkthrough.gif" alt-text="Kusto Web Explorer エクスペリエンスのチュートリアル":::

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 持っていない場合は、始める前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* クラスターと、データが含まれるデータベース。 [独自のクラスターを作成する](create-cluster-database-portal.md)か、Azure Data Explorer のヘルプ クラスターを使用します。

## <a name="sign-in-to-the-application"></a>アプリケーションにサインインする

[アプリケーション](https://dataexplorer.azure.com/)にサインインします。

## <a name="add-clusters"></a>クラスターを追加する

最初にアプリケーションを開いたときには、クラスターには接続していません。

![クラスターを追加する](media/web-query-data/add-cluster.png)

クエリの実行を始める前に、クラスターへの接続を追加する必要があります。 このセクションでは、Azure Data Explorer **ヘルプ** クラスターと、[前提条件](#prerequisites)で作成したテスト クラスター (省略可能) への接続を追加します。

### <a name="add-help-cluster"></a>ヘルプ クラスターを追加する

1. アプリケーションの左上にある **[クラスターの追加]** を選択します。

1. **[クラスターの追加]** ダイアログ ボックスで URI https://help.kusto.windows.net を入力して、 **[追加]** を選択します。
   
1. 左側のウィンドウに **help** クラスターが表示されます。 **Samples** データベースを展開し、**Tables** フォルダーを開いて、アクセスできるサンプル テーブルを表示します。

    :::image type="content" source="media/web-query-data/help-cluster.png" alt-text="ヘルプ クラスターでテーブルを検索する":::

このクイック スタートでこの後、および他の Azure Data Explorer 記事では、**StormEvents** テーブルを使用します。 

### <a name="add-your-cluster"></a>クラスターを追加する

作成したテスト クラスターを追加します。

1. **[クラスターの追加]** を選択します。

1. **[クラスターの追加]** ダイアログ ボックスで、テスト クラスターの URL を `https://<ClusterName>.<Region>.kusto.windows.net/` の形式で入力して､ **[追加]** を選択します。 たとえば、次の図のように、`https://mydataexplorercluster.westus.kusto.windows.net` と指定します。

    :::image type="content" source="media/web-query-data/server-uri.png" alt-text="テスト クラスターの URL を入力する":::
    
1. 次の例では、**help** クラスターと、新しいクラスター **docscluster.westus** (完全な URL は `https://docscluster.westus.kusto.windows.net/`) が表示されています。

    ![テスト クラスター](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>クエリを実行する

これで、両方のクラスターでクエリを実行できるようになりました (テスト クラスターにデータがあるものとします)。 この記事では、**help** クラスターに焦点を当てます。

1. 左側のウィンドウで **help** クラスターの下の **Samples** データベースを選択します。

1. 次のクエリをコピーしてクエリ ウィンドウに貼り付けます。 ウィンドウの上部にある **[Run]\(実行\)** を選択します。

    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    
    このクエリを実行すると、**StormEvents** テーブルから最新の 10 レコードが返されます。 結果は次のテーブルのようになります。

    :::image type="content" source="media/web-query-data/result-set-take-10.png" alt-text="10 個の嵐イベントのデータが一覧表示されているテーブルのスクリーンショット。" border="false":::

    次の図には、クラスターが追加されたアプリケーションの状態、およびクエリと結果が示されています。

    :::image type="content" source="media/web-query-data/webui-take10.png" alt-text="全画面表示":::

1. 次のクエリをコピーし、クエリ ウィンドウの最初のクエリの下に貼り付けます。 最初のクエリのように異なる行に書式設定されていないことに注意してください。

    ```kusto
    StormEvents | sort by StartTime desc 
    | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. 新しいクエリを選択します。 *Shift + Alt + F* キーを押して、クエリを次のように書式設定します。

    ![書式設定されたクエリ](media/web-query-data/formatted-query.png)

1. **[実行]** を選択するか、*Shift + Enter* キーを押して、クエリを実行します。 このクエリでは 1 番目と同じレコードが返されますが、`project` ステートメントで指定されている列のみが含まれます。 結果は次のテーブルのようになります。

    :::image type="content" source="media/web-query-data/result-set-project.png" alt-text="10 個の嵐イベントの開始時刻、終了時刻、州、イベントの種類、被害の特性、体験談のエピソードの一覧が表示されているテーブルのスクリーンショット。" border="false":::

    > [!TIP]
    > クエリを再実行する必要なしに、最初のクエリの結果セットを表示するには、クエリ ウィンドウの上部の **[Recall]\(リコール\)** を選択します。 分析では複数のクエリを実行することがよくあり、 **[Recall]\(リコール\)** を使用すると前のクエリの結果を取得できます。

1. もう 1 つクエリを実行し、異なる種類の出力を見てみましょう。

    ```kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```

    結果は次のグラフのようになります。

    ![縦棒グラフ](media/web-query-data/column-chart.png)

    > [!NOTE]
    > クエリ式の空白行は、クエリのどの部分が実行されるかに影響を与える可能性があります。
    > * テキストが選択されていない場合は、クエリまたはコマンドが空白行で区切られていると想定されます。
    > * テキストが選択されている場合は、選択したテキストが実行されます。

## <a name="work-with-the-table-grid"></a>テーブル グリッドを使用する

基本的なクエリの動作を確認したので、次にテーブル グリッドを使用して結果をカスタマイズし、さらに分析を行うことができます。 

### <a name="expand-a-cell"></a>セルを展開する

セルの展開は、長い文字列や JSON などの動的フィールドを表示する場合に便利です。 

1. セルをダブルクリックすると、展開されたビューが開きます。 このビューでは、長い文字列を読み取ることができ、動的データの JSON 形式が提供されます。

    :::image type="content" source="media/web-query-data/expand-cell.png" alt-text="長い文字列を表示するために Azure Data Explorer WebUI でセルを展開する":::

1. 読み取りペインのモードを切り替えるには、結果グリッドの右上にあるアイコンをクリックします。 展開ビューの読み取りペインのモードは、[インライン]、[下] ペイン、および [右] ペインから選択します。

    :::image type="content" source="media/web-query-data/expanded-view-icon.png" alt-text="展開ビューの読み取りペインのモードを変更するアイコン - Azure Data Explorer WebUI のクエリ結果":::

### <a name="expand-a-row"></a>Expand a row

数十の列を含むテーブルを操作する場合は、行全体を展開して、さまざまな列とその内容の概要を簡単に確認できるようにします。 

1. 展開する行の左側の矢印 **>** をクリックします。

    :::image type="content" source="media/web-query-data/expand-row.png" alt-text="Azure Data Explorer WebUI で行を展開する":::

1. 展開された行では、一部の列は展開され (下向きの矢印)、一部の列は折りたたまれています (右向きの矢印)。 これらの矢印をクリックすると、これらの 2 つのモードが切り替わります。

### <a name="group-column-by-results"></a>結果ごとに列をグループ化する

結果内において、任意の列で結果をグループ化できます。

1. 次のクエリを実行します。
     
    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

1. **State** 列をマウスでポイントし、メニューを選択して、 **[Group by State]\(State でグループ化\)** を選択します。

    ![Group by State (State でグループ化)](media/web-query-data/group-by.png)

1. グリッドで **California** をダブルクリックして展開し、その州のレコードを表示します。 この種類のグループ化は、探索的分析を行うときに役に立ちます。

    :::image type="content" source="media/web-query-data/group-expanded.png" alt-text="California グループが展開されたクエリ結果グリッドのスクリーンショット" border="false":::

1. **[Group]\(グループ\)** 列をマウスでポイントし、 **[Reset columns]\(列のリセット\)** を選択します。 この設定により、グリッドは元の状態に戻ります。

    ![Reset columns (列のリセット)](media/web-query-data/reset-columns.png)

#### <a name="use-value-aggregation"></a>値の集計を使用する

列でグループ化した後、値の集計関数を使用して、グループごとの単純な統計を計算できます。

1. 評価する列のメニューを選択します。
1. **[Value Aggregation]\(値の集計\)** を選択し、この列に対して実行する関数の種類を選択します。

    :::image type="content" source="media/web-query-data/aggregate.png" alt-text="結果ごとに列をグループ化するときに結果を集計する。 ":::

### <a name="filter-columns"></a>列をフィルター処理する

1 つまたは複数の演算子を使用して、列の結果をフィルター処理できます。

1. 特定の列をフィルター処理するには、その列のメニューを選択します。
1. フィルター アイコンを選択します。
1. フィルター ビルダーで、目的の演算子を選択します。
1. 列をフィルターする式を入力します。 入力するにつれて結果がフィルター処理されます。
    
    > [!NOTE] 
    > フィルターでは大文字と小文字は区別されません。

1. 複数条件フィルターを作成するには、ブール演算子を選択して別の条件を追加します
1. フィルターを削除するには、最初のフィルター条件からテキストを削除します。

    :::image type="content" source="media/web-query-data/filter-column.gif" alt-text="Azure Data Explorer WebUI で列に対してフィルター処理を行う方法を示す GIF":::

### <a name="run-cell-statistics"></a>セルの統計を実行する

1. 次のクエリを実行します。

    ```kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. 結果グリッドで、いくつかの数値セルを選択します。 テーブル グリッドを使用して、複数の行、列、セルを選択し、それらの集計を計算することができます。 現在、Web UI でサポートされている数値関数は次のとおりです: **Average**、**Count**、**Min**、**Max**、**Sum**。

    :::image type="content" source="media/web-query-data/select-stats.png" alt-text="関数を選択する"::: 

### <a name="filter-to-query-from-grid"></a>グリッドからのクエリへのフィルター処理

グリッドをフィルター処理するもう 1 つの簡単な方法は、グリッドから直接、クエリにフィルター演算子を追加することです。

1. クエリ フィルターを作成する対象の内容が含まれているセルを選択します。
1. 右クリックしてセル アクション メニューを開きます。 **[Add selection as filter]\(選択内容をフィルターとして追加\)** を選択します。
    
    :::image type="content" source="media/web-query-data/add-selection-filter.png" alt-text="Azure Data Explorer WebUI でグリッド結果からクエリを実行するための [Add selection as filter]\(選択内容をフィルターとして追加\)":::

1. クエリ エディターでクエリにクエリ句が追加されます。

    :::image type="content" source="media/web-query-data/add-query-from-filter.png" alt-text="Azure Data Explorer WebUI でグリッドのフィルター処理からクエリ句を追加する":::

### <a name="pivot"></a>ピボット

ピボット モード機能は Excel のピボット テーブルに似ており、グリッド自体で高度な分析を行うことができます。

ピボットを使用すると、列の値を取得して、列に変換することができます。 たとえば、*State* でピボットして、フロリダ、ミズーリ、アラバマなどの列を作成できます。

1. グリッドの右側にある **[列]** を選択して、テーブル ツール パネルを表示します。

    ![テーブル ツール パネル](media/web-query-data/tool-panel.png)

1. **[Pivot Mode]\(ピボット モード\)** を選択し、**EventType** 列を **[Row groups]\(行グループ\)** に、**DamageProperty** 列を **[Values]\(値\)** に、**State** 列を **[Column labels]\(列ラベル\)** に、それぞれドラッグします。  

    ![ピボット モード](media/web-query-data/pivot-mode.png)

    結果は次のピボット テーブルのようになります。

    ![ピボット テーブル](media/web-query-data/pivot-table.png)

## <a name="search-in-the-results-table"></a>結果テーブルで検索する

結果テーブル内で特定の式を検索できます。

1. 次のクエリを実行します。

    ```Kusto
    StormEvents
    | where DamageProperty > 5000
    | take 1000
    ```

1. 右側の **[検索]** ボタンをクリックし、「*Wabash*」と入力します

    :::image type="content" source="media/web-query-data/search.png" alt-text="テーブル内の検索":::

1. 検索した式のすべてのメンションが、テーブルで強調表示されます。 これらの間を移動するには、*Enter* キーを押すと前方に移動し、*Shift + Enter* キーを押すと後方に移動します。検索ボックスの横にある "*上向き*" と "*下向き*" のボタンを使用することもできます。

    :::image type="content" source="media/web-query-data/search-results.png" alt-text="検索結果の移動":::

## <a name="share-queries"></a>クエリを共有する

作成したクエリを共有したいことがよくあります。 

1. クエリ ウィンドウで、最初にコピーしたクエリを選択します。

1. クエリ ウィンドウの上部にある **[Share]\(共有\)** を選択します。 

    :::image type="content" source="media/web-query-data/share-menu.png" alt-text="共有メニュー":::

ドロップダウンでは、次のオプションを使用できます。
* リンクをクリップボードに保存
* [リンク、クエリをクリップボードに保存](#provide-a-deep-link)
* リンク、クエリ、結果をクリップボードに保存
* [ダッシュボードにピン留めする](#pin-to-dashboard)
* [Power BI へのクエリ](power-bi-imported-query.md)

### <a name="provide-a-deep-link"></a>ディープ リンクを提供する

ディープ リンクを提供して、クラスターにアクセスできる他のユーザーがクエリを実行できるようにすることができます。

1. **[共有]** で、 **[Link, query to clipboard]\(リンク、クエリをクリップボードに\)** を選択します。

1. リンクとクエリをテキスト ファイルにコピーします。

1. リンクを新しいブラウザー ウィンドウに貼り付けます。 結果は次のようになります

    :::image type="content" source="media/web-query-data/shared-query.png" alt-text="共有クエリのディープ リンク":::

### <a name="pin-to-dashboard"></a>[ダッシュボードにピン留めする]

Web UI でクエリを使用してデータ探索を完了し、必要なデータを見つけたら、それをダッシュボードにピン留めして継続的な監視を行うことができます。 

クエリをピン留めするには:

1. **[共有]** で、 **[ダッシュボードにピン留めする]** を選択します。

1. **[ダッシュボードにピン留めする]** ペインで、次のようにします。
    1. **[クエリ名]** を入力します。
    1. **[既存のものを使用]** または **[新規作成]** を選択します。
    1. **[ダッシュボード名]** を入力します。
    1. **[View dashboard after creation]\(作成後にダッシュボードを表示する\)** チェックボックスをオンにします (新しいダッシュボードの場合)。
    1. **[ピン留め]** の選択

    :::image type="content" source="media/web-query-data/pin-to-dashboard.png" alt-text="[ダッシュボードにピン留めする] ペイン":::

> [!NOTE]
> **[ダッシュボードにピン留めする]** では、選択したクエリのみがピン留めされます。 ダッシュボードのデータ ソースを作成し、レンダリング コマンドをダッシュボードでビジュアルに変換するには、データベースの一覧で、関連するデータベースを選択する必要があります。

## <a name="export-query-results"></a>クエリ結果をエクスポートする

クエリ結果を CSV ファイルにエクスポートするには、 **[ファイル]**  >  **[CSV にエクスポート]** を選択します。

:::image type="content" source="media/web-query-data/export-results.png" alt-text="結果を CSV ファイルにエクスポートする":::

## <a name="settings"></a>設定

**[設定]** タブでは、次のことができます。

* [環境の設定をエクスポートする](#export-environment-settings)
* [環境の設定をインポートする](#import-environment-settings)
* [エラーのレベルで強調表示する](#highlight-error-levels)
* [ローカル状態をクリアする](#clean-up-resources)

右上にある設定アイコン :::image type="icon" source="media/web-query-data/settings-icon.png" border="false"::: を選択して、 **[設定]** ウィンドウを開きます。

:::image type="content" source="media/web-query-data/settings.png" alt-text="[設定] ウィンドウ":::

### <a name="export-and-import-environment-settings"></a>環境の設定をエクスポートおよびインポートする

エクスポートとインポートの操作は、作業環境を保護し、他のブラウザーやデバイスに再配置するのに役立ちます。 エクスポート操作を使用すると、すべての設定、クラスター接続、およびクエリ タブが、別のブラウザーまたはデバイスにインポートできる JSON ファイルにエクスポートされます。

#### <a name="export-environment-settings"></a>環境の設定をエクスポートする

1. **[設定]**  >  **[全般]** ウィンドウで、 **[エクスポート]** を選択します。
1. **adx-export.json** ファイルがローカル ストレージにダウンロードされます。
1. 環境を元の状態に戻すには、 **[Clear local state]\(ローカル状態のクリア\)** を選択します。 これを設定すると、すべてのクラスター接続が削除され、開いているタブが閉じられます。

> [!NOTE]
> **[エクスポート]** を使用すると、クエリ関連のデータのみがエクスポートされます。 **adx-export.json** ファイルには、ダッシュボードのデータはエクスポートされません。

#### <a name="import-environment-settings"></a>環境の設定をインポートする

1. **[設定]**  >  **[全般]** ウィンドウで、 **[インポート]** を選択します。 その後、 **[警告]** ポップアップで、 **[インポート]** を選択します。

    :::image type="content" source="media/web-query-data/import.png" alt-text="インポートの警告":::

1. ローカル ストレージから **adx-export.json** ファイルを見つけて開きます。
1. 以前のクラスター接続と開いていたタブが使用できるようになります。

> [!NOTE]
> **[インポート]** を使用すると、既存の環境設定とデータが上書きされます。

### <a name="highlight-error-levels"></a>エラーのレベルで強調表示する

Kusto では、結果パネルの各行の重大度または詳細レベルを解釈し、それに応じて色の設定を試みます。 これは、各列の個別の値を一連の既知のパターン ("警告"、"エラー" など) と照合することによって行われます。 

#### <a name="enable-error-level-highlighting"></a>エラーのレベルでの強調表示を有効にする

エラーのレベルでの強調表示を有効にするには、以下を行います。

1. ユーザー名の隣にある **[設定]** アイコンを選択します。
1. **[表示]** タブを選択し、 **[エラーのレベルでの強調表示を有効にする]** オプションを右に切り替えます。 

    :::image type="content" source="media/web-query-data/enable-error-highlighting.gif" alt-text="設定でエラーのレベルでの強調表示を有効にする方法を示しているアニメーション GIF":::

**ライト** モードでのエラー レベルの配色 | **ダーク** モードでのエラー レベルの配色
|---|---|
:::image type="content" source="media/web-query-data/light-mode.png" alt-text="ライト モードでの色の凡例のスクリーン ショット"::: | :::image type="content" source="media/web-query-data/dark-mode.png" alt-text="ダーク モードでの色の凡例のスクリーン ショット":::

#### <a name="column-requirements-for-highlighting"></a>強調表示のため列の要件

強調表示のエラーのレベルの場合、列の型は int、long、または string である必要があります。 

* 列の型が `long` または `int` の場合は、次のようになります。
   * 列名は *Level* である必要があります。
   * 値には 1 から 5 までの数字のみを含めることができます。
* 列の型が `string` の場合は、次のようになります。 
   * パフォーマンスを向上させるために、必要に応じて列名を *Level* にすることができます。 
   * 列には、次の値のみを含めることができます。
       * critical、crit、fatal、assert、high
       * error、e
       * warning、w、monitor
       * 情報
       * verbose、verb、d
   
## <a name="provide-feedback"></a>フィードバックの提供

1. アプリケーションの右上にあるフィードバック アイコン :::image type="icon" source="media/web-query-data/icon-feedback.png" border="false"::: を選択します。

1. フィードバックを入力し、 **[Submit]\(送信\)** を選択してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートではリソースは何も作成しませんでしたが、アプリケーションから一方または両方のクラスターを削除したい場合は、クラスターを右クリックして、 **[Remove connection]\(接続を削除\)** を選択します。
もう 1 つのオプションとしては、 **[設定]**  >  **[全般]** タブから、 **[Clear local state]\(ローカル状態のクリア\)** を選択します。この操作を行うと、すべてのクラスター接続が削除され、開いているすべてのクエリ タブが閉じられます。

## <a name="next-steps"></a>次のステップ

[Azure Data Explorer のクエリを記述する](write-queries.md)
