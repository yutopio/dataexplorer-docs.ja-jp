---
title: Azure Data Explorer でデータのクエリを実行するための Power Apps アプリケーションを作成する
description: Azure Data Explorer のデータに基づいて Power Apps でアプリケーションを作成する方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 4b74b3aa7a765f6d54454e84dfeaeac2f6bfedd6
ms.sourcegitcommit: 88291fd9cebc26e5210463cb95be5540bf84eef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437510"
---
# <a name="create-power-apps-application-to-query-data-in-azure-data-explorer-preview"></a>Azure Data Explorer でデータのクエリを実行するための Power Apps アプリケーションを作成する (プレビュー)

Azure Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するためのフル マネージド データ分析サービスです。

Power Apps は、ビジネス データに接続するカスタム アプリを構築するための RAD (Rapid Application Development) 環境を提供する、アプリ、サービス、コネクタ、データ プラットフォームのスイートです。 Power Apps コネクタは、Azure Data Explorer に大規模かつ増大しているストリーミング データのコレクションがあり、このデータを利用するためにロー コードで高機能なアプリを開発する場合に特に便利です。 この記事では、Azure Data Explorer のデータのクエリを実行するための Power Apps アプリケーションを作成します。 このプロセスでは、データのパラメータ化、取得、表示の手順を実行します。

## <a name="prerequisites"></a>前提条件

* Power Platform のライセンス。 [https://powerapps.microsoft.com](https://powerapps.microsoft.com) で開始します。
* [Power Apps スイート](https://docs.microsoft.com/powerapps/powerapps-overview)に関する知識。

## <a name="connect-to-azure-data-explorer-connector"></a>Azure Data Explorer コネクタに接続する

1. [https://make.preview.powerapps.com/](https://make.preview.powerapps.com/) に移動してサインインします。

1. 左側のメニューで **[接続]** を選択します。
1. **[+ 新しい接続]** を選択します。

    :::image type="content" source="media/power-apps-connector/new-connection.png" alt-text="Power Apps での新しい接続の作成":::

1. 検索バーで **Azure Data Explorer** を検索します。 結果の選択肢から、 **[Azure Data Explorer]** を選択します。

    :::image type="content" source="media/power-apps-connector/search-adx.png" alt-text="Power Apps での新しい接続の作成":::

1. **[Azure Data Explorer]** ポップアップで **[作成]** を選択します。 必要に応じて資格情報を指定します。

    :::image type="content" source="media/power-apps-connector/create-connector.png" alt-text="Power Apps での新しい接続の作成":::

## <a name="create-app"></a>アプリを作成する

1. Power Apps に移動し、左側のメニューで **[アプリ]** を選択します。
1. メニュー バーで **[+ 新しいアプリ]** を選択します。
1. 表示されたドロップダウンから **[キャンバス]** を選択します。

    :::image type="content" source="media/power-apps-connector/create-new-app.png" alt-text="Power Apps での新しい接続の作成":::

1. **[空のアプリ]** セクションで **[タブレット レイアウト]** を選択します。

    :::image type="content" source="media/power-apps-connector/blank-canvas.png" alt-text="Power Apps での新しい接続の作成":::

### <a name="add-connector"></a>コネクタを追加する

1. 左側のナビゲーションで、 **データ** アイコンをクリックします。 
1. **[コネクタ]** を展開します。
1. 表示されたオプションから **[Azure Data Explorer]** を選択します。

    :::image type="content" source="media/power-apps-connector/data-connectors-adx.png" alt-text="Power Apps での新しい接続の作成":::

**[アプリ内]** という新しい領域が表示され、 **[Azure Data Explorer]** が含まれています。

   :::image type="content" source="media/power-apps-connector/adx-appears.png" alt-text="Power Apps での新しい接続の作成":::

### <a name="save-your-app"></a>アプリを保存する

1. メニュー バーで **[ファイル]** を選択します。 
1. 左側のナビゲーションで **[保存]** を選択します。

    :::image type="content" source="media/power-apps-connector/save-app.png" alt-text="Power Apps での新しい接続の作成":::

1. アプリのわかりやすい名前を入力します。 右下にある **[保存]** ボタンをクリックします。

### <a name="advanced-settings"></a>詳細設定

1. 左側のメニューにある **[設定]** を選択します。
1. **[詳細設定]** を選択します。
1. 表示されたオプションから **[動的スキーマ]** を選択します。 この機能を有効にします。

    :::image type="content" source="media/power-apps-connector/dynamic-schema.png" alt-text="Power Apps での新しい接続の作成":::

1. **[委任できないクエリのデータ行の制限]** 設定を検索します。 返されるレコード数の制限を設定します。

    :::image type="content" source="media/power-apps-connector/set-limit.png" alt-text="Power Apps での新しい接続の作成":::

    > [!NOTE]
    > 返されるレコードの既定の制限は 500 で、最大は 2,000 です。

> [!IMPORTANT]
> アプリをもう一度保存し、必要に応じて再起動します。

### <a name="add-dropdown"></a>ドロップダウンを追加する

1. メニュー バーで **[挿入]** を選択します。 
1. 表示されたサブ メニュー バーで **[入力]** を選択します。 
1. 表示されたドロップダウンで **[ドロップ ダウン]** を選択します。
1. 右側のポップアウトで **[詳細設定]** タブをクリックします。
1. **[Items]\(アイテム\)** 入力ボックスに「["CALIFORNIA","MICHIGAN"]」を入力します。

    :::image type="content" source="media/power-apps-connector/populate-dropdown.png" alt-text="Power Apps での新しい接続の作成" lightbox="media/power-apps-connector/populate-dropdown.png":::

1. **ドロップダウン** を選択したまま、数式バーの **[プロパティ]** ドロップダウンから **[OnChange]** を選択します。

1. 次の式を入力します。

    ```kusto
    ClearCollect(
    Results,
    AzureDataExplorer.listKustoResultsPost(
    "https://help.kusto.windows.net",
    "Samples",
    "StormEvents | where State == '" & Dropdown1.SelectedText.Value & "' | take 15"
    ).value
    )
    ```
    
1. **[スキーマのキャプチャ]** ボタンをクリックします。 処理が終わるのを待ちます。

    :::image type="content" source="media/power-apps-connector/capture-schema.png" alt-text="Power Apps での新しい接続の作成":::

### <a name="add-data-table"></a>データ テーブルを追加する

1. メニュー バーで **[挿入]** を選択します。 
1. 表示されたサブ メニュー バーで **[データ テーブル]** を選択します。
1. データ テーブルの位置を変更し、見やすいように罫線を追加することを検討します。
1. 右側のポップアウトで **[プロパティ]** タブを選択します。 **[Data Source]\(データ ソース\)** ドロップダウンから [Results]\(結果\) を選択します。
1. **[フィールドの編集]** リンクを選択します。 
1. 表示されたポップアウトで **[+ フィールドの追加]** を選択します。 
    
    :::image type="content" source="media/power-apps-connector/insert-data-table-small.png" alt-text="Power Apps での新しい接続の作成" lightbox="media/power-apps-connector/insert-data-table.png":::

1. 目的のフィールドを選択し、 **[追加]** ボタンをクリックします。 選択したデータ テーブルのプレビューが表示されます。

    :::image type="content" source="media/power-apps-connector/preview-table.png" alt-text="Power Apps での新しい接続の作成":::

### <a name="validate"></a>検証

1. 画面の右上にある **[Preview the app]\(アプリのプレビュー\)** ボタンをクリックします。
1. ドロップダウンを選択してみたり、データ テーブルをスクロールしたりして、データの取得と表示が正常に行われることを確認します。

    :::image type="content" source="media/power-apps-connector/preview-app.png" alt-text="Power Apps での新しい接続の作成":::

### <a name="limitations"></a>制限事項

* Power Apps には、クライアントに返される結果のレコード数が最大で 2,000 件という制限があります。 これらのレコードの合計メモリの上限は 64 MB、実行時間の上限は 7 分です。
* コネクタは、[フォーク](https://docs.microsoft.com/azure/data-explorer/kusto/query/forkoperator)演算子と[ファセット](https://docs.microsoft.com/azure/data-explorer/kusto/query/facetoperator)演算子をサポートしていません。
* **タイムアウトの例外** :コネクタのタイムアウト制限は 7 分間です。 タイムアウトが発生する可能性を回避するには、クエリを効率化して実行速度を上げるか、チャンクに分割します。 各チャンクは、クエリの別のパートで実行可能です。 詳細については、「[クエリのベスト プラクティス](https://docs.microsoft.com/azure/data-explorer/kusto/query/best-practices)」を参照してください。

## <a name="next-steps"></a>次のステップ
