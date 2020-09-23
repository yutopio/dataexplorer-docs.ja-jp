---
title: Grafana を使用して Azure Data Explorer のデータを視覚化する
description: この記事では、Azure Data Explorer を Grafana のデータ ソースとして設定し、サンプル クラスターのデータを視覚化する方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 08093fd06fed1facc1d8e55d98785abb952632c8
ms.sourcegitcommit: 95527c793eb873f0135c4f0e9a2f661ca55305e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534074"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Grafana で Azure Data Explorer のデータを視覚化する

Grafana は、データのクエリを実行して視覚化し、視覚化に基づいてダッシュボードを作成して共有できる分析プラットフォームです。 Grafana には、Azure Data Explorer に接続してそのデータを視覚化できる、Azure Data Explorer "*プラグイン*" が用意されています。 この記事では、Azure Data Explorer を Grafana のデータ ソースとして設定し、サンプル クラスターのデータを視覚化する方法について説明します。

Grafana の Azure Data Explorer プラグインを使用し、Azure Data Explorer を Grafana 用のデータ ソースとして設定して、データを視覚化する方法を学習するには、次のビデオを使用します。 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

代わりに、以下の記事で詳しく説明されているように、[データ ソースを構成](#configure-the-data-source)し、[データを視覚化](#visualize-data)することができます。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* お使いのオペレーティング システムに対応する [Grafana バージョン 5.3.0 以降](https://docs.grafana.org/installation/)。

* Grafana の [Azure Data Explorer プラグイン](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)。 Grafana クエリ ビルダーを使用するには、プラグイン バージョン 3.0.5 以降が必要です。

* StormEvents サンプル データを含むクラスター。 詳細については、「[クイック スタート:Azure Data Explorer クラスターとデータベースを作成する](create-cluster-database-portal.md)」、および「[Azure のデータ エクスプローラーにサンプル データを取り込む](ingest-sample-data.md)」を参照してください。

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>プロパティを指定し、接続をテストする

"*閲覧者*" ロールに割り当てられたサービス プリンシパルを使用して、Grafana のインスタンスでプロパティを指定し、Azure Data Explorer への接続をテストします。

1. Grafana で、左側のメニューの歯車アイコンを選択し、**[Data Sources]\(データ ソース\)** を選択します。

    ![データ ソース](media/grafana/data-sources.png)

1. **[Add data source (データ ソースの追加)]** を選択します。

1. **[Data Sources / New]\(データソース/新規\)** ページで、データ ソースの名前を入力し、種類として **[Azure Data Explorer Datasource]\(Azure Data Explorer データ ソース\)** を選択します。

    ![接続名と種類](media/grafana/connection-name-type.png)

1. https://{クラスター名}.{リージョン}.kusto.windows.net の形式でクラスターの名前を入力します。 Azure portal または CLI で取得した他の値を入力します。 マッピングについては、次の画像の下の表をご覧ください。

    ![接続のプロパティ](media/grafana/connection-properties.png)

    | Grafana UI | Azure portal | Azure CLI |
    | --- | --- | --- |
    | サブスクリプション ID | サブスクリプション ID | SubscriptionId |
    | テナント ID | ディレクトリ ID | tenant |
    | クライアント ID | アプリケーション ID | appId |
    | クライアント シークレット | パスワード | password |
    | | | |

1. **[Save & Test]\(保存してテスト\)** を選択します。

    テストが成功した場合は、次のセクションに進みます。 問題が発生した場合は、Grafana で指定した値を確認し、これまでの手順を見直します。

## <a name="visualize-data"></a>データの視覚化

Azure Data Explorer を Grafana のデータ ソースとして構成したら、データを視覚化します。 クエリ ビルダー モードとクエリ エディターの raw モードの両方を使用した基本的な例を紹介します。 サンプル データ セットに対して実行する他のクエリの例については、「[Azure データ エクスプローラーのクエリを記述する](write-queries.md)」を参照することをお勧めします。

1. Grafana で、左側のメニューのプラス アイコンを選択し、**[Dashboard]\(ダッシュボード\)** を選択します。

    ![ダッシュボードの作成](media/grafana/create-dashboard.png)

1. **[Add]\(追加\)** タブの **[Add new panel]\(新しいパネルの追加\)** を選択します。

    ![グラフの追加](media/grafana/add-graph.png)

1. グラフ パネルで、**[Panel Title]\(パネル タイトル\)**、**[Edit]\(編集\)** の順に選択します。

    ![パネルの編集](media/grafana/edit-panel.png)

1. パネルの下部にある **[Data Source]\(データ ソース\)** を選択し、構成済みのデータ ソースを選択します。

    ![データ ソースの選択](media/grafana/select-data-source.png)

### <a name="query-builder-mode"></a>クエリ ビルダーモード

クエリ エディターには 2 つのモードがあります。 クエリ ビルダー モードと raw モードです。 クエリ ビルダー モードを使用してクエリを定義します。

1. データ ソースの下で、 **[データベース]** を選択し、ドロップダウンからお使いのデータベースを選択します。 
1. **[From]\(ソース\)** を選択し、ドロップダウンからお使いのテーブルを選択します。

    :::image type="content" source="media/grafana/query-builder-from-table.png" alt-text="クエリ ビルダーでのテーブルの選択":::    

1. テーブルが定義されたら、データをフィルター処理し、表示する値を選択して、それらの値のグループ化を定義します。

    **フィルター**
    1. **[Where (filter)]\(場所 (フィルター)\)** の右にある **+** をクリックして、テーブル内の 1 つ以上の列をドロップダウンから選択します。 
    1. フィルターごとに、該当する演算子を使用して値を定義します。 
    この選択は、Kusto クエリ言語で [where 演算子](kusto/query/whereoperator.md)を使用する場合と似ています。

    **値の選択**
    1. **[value columns]\(値の列\)** の右にある **+** をクリックして、パネルに表示する値の列をドロップダウンから選択します。
    1. 値の列ごとに、集計の種類を設定します。 
    1 つまたは複数の値の列を設定できます。 この選択は、[summarize 演算子](kusto/query/summarizeoperator.md)を使用することと同じです。

    **値のグループ化** <br> 
    **[Group by (summarize)]\(グループ化 (集計)\)** の右にある **+** をクリックして、値をグループに配置するために使用する 1 つ以上の列をドロップダウンから選択します。 これは、summarize 演算子のグループ式と同じです。

1. クエリを実行するには **[クエリの実行]** を選択します。

    :::image type="content" source="media/grafana/query-builder-all-values.png" alt-text="すべての値が入力されたクエリ ビルダー":::

    > [!TIP]
    > クエリ ビルダーで設定を確定する間に、Kusto クエリ言語クエリが作成されます。 このクエリは、グラフィカル クエリ エディターで構築したロジックを示します。 

1. raw モードに移行し、Kusto クエリ言語の柔軟性と能力を使用してクエリを編集するには、 **[KQL の編集]** を選択します。

:::image type="content" source="media/grafana/query-builder-with-raw-query.png" alt-text="未加工クエリを使用したクエリ ビルダー":::

### <a name="raw-mode"></a>raw モード

raw モードを使用してクエリを編集します。 

1. クエリ ペインで、次のクエリをコピーし、 **[クエリの実行]** を選択します。 このクエリは、サンプル データ セットのイベント数を日単位でバケット処理します。

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Run query](media/grafana/run-query.png)

1. グラフは既定で過去 6 時間のデータを対象としているため、グラフには結果が表示されません。 上部のメニューで、**[Last 6 hours]\(過去 6 時間\)** を選択します。

    ![過去 6 時間](media/grafana/last-six-hours.png)

1. StormEvents サンプル データ セットに含まれている 2007 年を対象とするカスタム範囲を指定します。 **[適用]** を選択します。

    ![カスタム日付範囲](media/grafana/custom-date-range.png)

    これで、日単位でバケット処理された 2007 年のデータがグラフに表示されます。

    ![完成したグラフ](media/grafana/finished-graph.png)

1. 上部のメニューで保存アイコンを選択します。 ![[Save]\(保存\) アイコン](media/grafana/save-icon.png).

> [!IMPORTANT]
> クエリ ビルダー モードに切り替えるには、 **[Switch to builder]\(ビルダーに切り替え\)** を選択します。 Grafana は、クエリをクエリ ビルダーで使用可能なロジックに変換します。 クエリ ビルダーのロジックには制限があるため、クエリに対して手動で行った変更が失われる可能性があります。

:::image type="content" source="media/grafana/raw-mode.png" alt-text="raw モードからビルダーに移行する":::

## <a name="create-alerts"></a>アラートを作成する

1. ホームダッシュボードで、 **[アラート]**  >  **[通知チャネル]** の順に選択して、新しい通知チャネルを作成します。

    ![通知チャネルを作成する](media/grafana/create-notification-channel.png)

1. 新しい **[通知チャネル]** を作成して、**[保存]** します。

    ![新しい通知チャネルを作成する](media/grafana/new-notification-channel-adx.png)

1. **[ダッシュボード]** で、ドロップダウンから **[編集]** を選択します。

    ![ダッシュボードで [編集] を選択する](media/grafana/edit-panel-4-alert.png)

1. アラート ベル アイコンを選択して、**アラート** ウィンドウを開きます。 **[アラートの作成]** を選択します。 **アラート** ウィンドウで、次のプロパティを完了します。

    ![アラートのプロパティ](media/grafana/alert-properties.png)

1. **ダッシュボードの保存**アイコンを選択して、変更を保存します。

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer のクエリを記述する](write-queries.md)
