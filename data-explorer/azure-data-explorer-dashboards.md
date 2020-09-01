---
title: Azure Data Explorer ダッシュボードを使用してデータを視覚化する
description: Azure Data Explorer ダッシュボードを使用してデータを視覚化する方法について説明します
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/26/2020
ms.openlocfilehash: 0b5633dc7ed54f9b4a763400ae8de84ba32f09e6
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872592"
---
# <a name="visualize-data-with-azure-data-explorer-dashboards"></a>Azure Data Explorer ダッシュボードを使用してデータを視覚化する

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure Data Explorer には、クエリの実行とダッシュボードの構築を行える Web アプリケーションが用意されています。 ダッシュボードは、スタンドアロンの Web アプリケーションである [Web UI](web-query-data.md) で使用できます。 Azure Data Explorer は、[Power BI](power-bi-connector.md) や [Grafana](grafana.md) などの他のダッシュボード サービスとも統合されています。

Azure Data Explorer のダッシュボードには、次の 3 つの主な利点があります。

* Web UI から Azure Data Explorer ダッシュボードにクエリをネイティブでエクスポートできます。 
* Web UI でデータを探索できます。
* ダッシュボードのレンダリング パフォーマンスが最適化されています。

次の図は、Azure Data Explorer ダッシュボードを示しています。

:::image type="content" source="media/adx-dashboards/dash.png" alt-text="最終的なダッシュボード":::

## <a name="create-a-dashboard"></a>ダッシュボードを作成する

1. ナビゲーション バーで **[ダッシュボード]** を選択し、 **[新しいダッシュボード]** を選択します。

    :::image type="content" source="media/adx-dashboards/new-dashboard.png" alt-text="新しいダッシュボード":::

1. ダッシュボード名を選択し、 **[作成]** を選択します。

    :::image type="content" source="media/adx-dashboards/new-dashboard-popup.png" alt-text="ダッシュボードを作成する":::

## <a name="add-data-source"></a>データ ソースを追加する

ダッシュボードに必要なデータ ソースを追加します。

1. 上部のバーの **[データ ソース]** メニュー項目を選択します。 **[データ ソース]** ペインで、 **[+ 新しいデータ ソース]** ボタンを選択します。

    :::image type="content" source="media/adx-dashboards/data-source.png" alt-text="データ ソース":::

1. **[新しいデータ ソースの作成]** ペインで、次のようにします。
    1. **[クラスター URI]** またはリージョンを含む部分的な名前を入力し、 **[接続]** を選択します。 
    1. ドロップダウン リストから **[データベース]** を選択します。
    1. 既定値を使用するか、必要に応じて **[データ ソース名]** を変更します。 
    1. **[適用]** を選択します。

    :::image type="content" source="media/adx-dashboards/data-source-pane.png" alt-text="[データ ソース] ウィンドウ":::

## <a name="use-parameters"></a>パラメーターを使用する

パラメーターを使用すると、ダッシュボード フィルターを使用できます。 パラメーターを使用すると、ダッシュボードのレンダリング パフォーマンスが大幅に向上し、クエリのなるべく早い段階でフィルター値を使用できるようになります。 パラメーターの使用方法の詳細については、「[Azure Data Explorer ダッシュボードでパラメーターを使用する](dashboard-parameters.md)」を参照してください。

1. 上部のバーで **[パラメーター]** を選択します。 **[パラメーター]** ペインで、 **[+ 新しいパラメーター]** ボタンを選択します。

    :::image type="content" source="media/adx-dashboards/parameters.png" alt-text="[新しいパラメーター] の選択":::

1. すべての必須フィールドに値を入力し、 **[完了]** を選択します。

    :::image type="content" source="media/adx-dashboards/parameter-pane.png" alt-text="[パラメーター] ペイン":::

|フィールド  |説明 |
|---------|---------|
|**Parameter display name (パラメーター表示名)**    |   ダッシュボードまたは編集カードに表示されるパラメーターの名前。      |
|**パラメーターの種類**    |次のいずれか:<ul><li>**Single selection (単一選択)** :パラメーターの入力としてフィルターで値を 1 つのみ選択できます。</li><li>**Multiple selection (複数選択)** :パラメーターの入力としてフィルターで値を 1 つまたは複数選択できます。</li><li>**[時間範囲]** : 時間に基づいてクエリとダッシュボードをフィルター処理するための追加パラメーターを作成できます。既定では、すべてのダッシュボードに時間範囲のピッカーがあります。</li></ul>    |
|**[変数名]**     |   クエリで使用されるパラメーターの名前。      |
|**データの種類**    |    パラメーター値のデータ型。     |
|**Pin as dashboard filter (ダッシュボード フィルターとしてピン留め)**   |   パラメーターベースのフィルターをダッシュボードにピン留めするか、ダッシュボードからピン留めを外します。       |
|**ソース**     |    パラメーター値のソース。 <ul><li>**Fixed values (固定値)** :手動で導入される静的なフィルター値。 </li><li>**Query**: KQL クエリを使用して動的に導入される値。  </li></ul>    |
|**Add a “Select all” value (「すべて選択」の値を追加)**    |   単一選択と複数選択のパラメーターの種類にのみ適用されます。 すべてのパラメーター値のデータを取得するために使用します。      |

## <a name="add-query"></a>クエリの追加

**クエリの追加**では、Kusto クエリ言語のスニペットを使用してデータを取得し、ビジュアルをレンダリングします。 各クエリは 1 つのビジュアルをサポートできます。

1. ダッシュボード キャンバスまたは上部のメニューバーから **[クエリの追加]** を選択します。

    :::image type="content" source="media/adx-dashboards/empty-dashboard-new-query.png" alt-text="新しいクエリ":::

1. **[クエリ]** ペインで、 
    1. ドロップダウンからデータ ソースを選択します
    1. クエリを入力し、 **[実行]** を選択します 
    1. **[+ Add visual]\(+ ビジュアルの追加\)** を選択します

    :::image type="content" source="media/adx-dashboards/initial-query.png" alt-text="クエリの実行":::

1. **[Visual formatting]\(ビジュアルの形式\)** ペインで、 **[グラフの種類]** を選択し、ビジュアルの種類を選択します。 
1. ビジュアルに名前を付け、 **[変更の適用]** を選択して、ビジュアルをダッシュボードにピン留めします。

    :::image type="content" source="media/adx-dashboards/add-visual.png" alt-text="ビジュアルをクエリに追加":::

1. ビジュアルのサイズを変更し、 **[変更を保存]** でダッシュボードを保存できます。

    :::image type="content" source="media/adx-dashboards/save-dashboard.png" alt-text="ダッシュボードを保存する":::

## <a name="next-steps"></a>次の手順

* [Azure Data Explorer ダッシュボードでパラメーターを使用する](dashboard-parameters.md)
* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md)
