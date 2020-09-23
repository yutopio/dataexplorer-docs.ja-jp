---
title: Power Automate に接続する Azure Data Explorer コネクタの使用例 (プレビュー)
description: Power Automate に接続して Azure Data Explorer コネクタを使用する一般的な例をいくつか取り上げて説明します。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/15/2020
ms.openlocfilehash: 03422de8987e125b5565b0625434ef660426b40a
ms.sourcegitcommit: c2ab3176db4dd55ac9ca8eee52bbd24096d1277f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2020
ms.locfileid: "90740271"
---
# <a name="usage-examples-for-azure-data-explorer-connector-to-power-automate-preview"></a>Power Automate に接続する Azure Data Explorer コネクタの使用例 (プレビュー)

Azure Data Explorer Power Automate (以前は Microsoft Flow) コネクタを使用すると、Azure Data Explorer で、[Microsoft Power Automate](https://flow.microsoft.com/) の Flow 機能を使用できます。 Kusto のクエリとコマンドを、スケジュール設定されたタスクまたはトリガーされたタスクの一環として、自動的に実行することができます。 この記事では、一般的な Power Automate コネクタの使用例をいくつか紹介します。

詳細については、[Azure Data Explorer Power Automate コネクタ (プレビュー)](flow.md) に関するページをご覧ください。

## <a name="power-automate-connector-and-your-sql-database"></a>Power Automate コネクタと SQL データベース

Power Automate コネクタを使用して、データに対してクエリを実行し、SQL データベースで集計します。

> [!Note]
> Power Automate コネクタは、少量の出力データにのみ使用してください。 SQL の挿入操作は、行ごとに個別に行われます。 

![Power Automate コネクタを使用したデータ クエリのスクリーンショット](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> **[クラスター名]** フィールドに、クラスターの URL を入力します。

## <a name="push-data-to-a-microsoft-power-bi-dataset"></a>Microsoft Power BI データセットにデータをプッシュする

Power Automate コネクタと Power BI コネクタを使用して、Kusto クエリから Power BI ストリーミング データセットにデータをプッシュすることができます。

1. 新しい **[クエリの実行と結果の一覧表示]** アクションを作成します。
1. **[新しいステップ]** を選択します。
1. **[アクションの追加]** を選択し、Power BI を検索します。
1. **[Power BI]**  >  **[データセットに行を追加する]** を選択します。 

    ![Power BI コネクタのスクリーンショット](./media/flow-usage/flow-powerbiconnector.png)

1. データのプッシュ先となる **[ワークスペース]** 、 **[データセット]** 、および **[テーブル]** を入力します。
1. 動的コンテンツのダイアログ ボックスで、データセット スキーマと関連する Kusto クエリ結果を含む**ペイロード**を追加します。

    ![Power BI フィールドのスクリーンショット](./media/flow-usage/flow-powerbifields.png)

Power BI アクションが、Kusto クエリ結果テーブルの各行に自動的に適用されます。 

![各行の Power BI アクションのスクリーンショット](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>条件付きクエリ

Kusto クエリの結果は、次の Power Automate アクションの入力または条件として使用できます。

次の例では、Kusto に対して、過去 1 日間に発生したインシデントのクエリを実行します。 解決されたインシデントごとに、Slack メッセージが投稿され、プッシュ通知が作成されます。
引き続きアクティブになっているインシデントごとに、Kusto に対して、同様のインシデントに関する詳細情報のクエリを実行します。 この情報はメールとして送信され、関連するタスクが Azure DevOps Server で開きます。

同様のフローを作成するには、以下の手順を実行します。

1. 新しい **[クエリの実行と結果の一覧表示]** アクションを作成します。
1. **[新しいステップ]**  >  **[条件コントロール]** の順に選択します。
1. 動的コンテンツのウィンドウで、次のアクションの条件として使用するパラメーターを選択します。
1. "*リレーションシップ*" と "*値*" の種類を選択して、特定のパラメーターに特定の条件を設定します。

    :::image type="content" source="./media/flow-usage/flow-condition.png" alt-text="次のフロー アクションを決定するために Kusto クエリの結果に基づいたフローの条件を使用、Azure Data Explorer" lightbox="./media/flow-usage/flow-condition.png#lightbox":::

    この条件は、クエリ結果テーブルの各行に適用されます。
1. 条件が true および false の場合のアクションを追加します。

    :::image type="content" source="./media/flow-usage/flow-conditionactions.png" alt-text="条件が true または false の場合のアクションの追加、Kusto クエリの結果に基づいたフローの条件、Azure Data Explorer" lightbox="./media/flow-usage/flow-conditionactions.png#lightbox":::

Kusto クエリの結果値を、次のアクションの入力として使用できます。 動的コンテンツのウィンドウで、結果値を選択します。
次の例では、 **[Slack - メッセージの投稿]** アクションと **[Visual Studio - 新しい作業項目を作成します]** アクションが追加しました。これには Kusto クエリのデータが含まれています。

![[Slack - メッセージの投稿] アクションのスクリーンショット](./media/flow-usage/flow-slack.png)

![Visual Studio アクションのスクリーンショット](./media/flow-usage/flow-visualstudio.png)

この例では、インシデントがまだアクティブである場合、Kusto に対してクエリを実行して、過去に同じソースからのインシデントが解決された方法に関する情報を取得します。

![フロー条件のクエリのスクリーンショット](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> **[クラスター名]** フィールドに、クラスターの URL を入力します。

この情報を円グラフとして視覚化し、チームにメールで送信します。

![フロー条件のメールのスクリーンショット](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>複数の Azure Data Explorer Flow のグラフをメールで送信する

1. 繰り返しトリガーを含む新しいフローを作成し、フローの間隔と頻度を定義します。 
1. 1 つ以上の **[Kusto - クエリの実行と結果の視覚化]** アクションを使用して、新しいステップを追加します。 

    ![フローでの複数クエリ実行のスクリーンショット](./media/flow-usage/flow-severalqueries.png)

1. **[Kusto - クエリの実行と結果の視覚化]** アクションごとに、次のフィールドを定義します。
    * クラスター URL。
    * データベース名。
    * クエリおよびグラフの種類 (HTML テーブル、円グラフ、時間グラフ、横棒グラフ、カスタム値など)。

    ![複数の添付ファイルを含む結果の視覚化のスクリーンショット](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. **[メールの送信 (v2)]** アクションを追加します。 
    1. 本文セクションで、コード ビュー アイコンを選択します。
    1. **[本文]** フィールドに必要な **BodyHtml** を挿入し、クエリの視覚化された結果がメールの本文に含まれるようにします。
    1. メールに添付ファイルを追加するには、 **[添付ファイル名]** と **[添付ファイルのコンテンツ]** を追加します。
    
    ![複数の添付ファイルのメール送信のスクリーンショット](./media/flow-usage/flow-email-multiple-attachments.png)

    メール アクション作成の詳細については、「[Kusto クエリの結果をメールで送信する](flow.md#email-kusto-query-results)」を参照してください。 

結果:

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments.png" alt-text="メールの複数の添付ファイルの結果、円グラフと横棒グラフで表示、Azure Data Explorer" lightbox="./media/flow-usage/flow-resultsmultipleattachments.png#lightbox":::

:::image type="content" source="./media/flow-usage/flow-resultsmultipleattachments2.png" alt-text="メールの複数の添付ファイルの結果、時間グラフとして表示、Azure Data Explorer" lightbox="./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox":::

## <a name="next-steps"></a>次のステップ

[Azure Kusto Logic App コネクタ](kusto/tools/logicapps.md)について確認します。この方法を使用して、Kusto のクエリとコマンドを、スケジュール設定されたタスクまたはトリガーされたタスクの一環として自動的に実行することもできます。

