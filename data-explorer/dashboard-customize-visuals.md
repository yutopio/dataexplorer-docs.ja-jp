---
title: Azure Data Explorer ダッシュボードのビジュアルをカスタマイズする
description: Azure Data Explorer ダッシュボードのビジュアルを簡単にカスタマイズする
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 22463307df0358228469fe480f5578222bed52bf
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379766"
---
# <a name="customize-azure-data-explorer-dashboard-visuals"></a>Azure Data Explorer ダッシュボードのビジュアルをカスタマイズする

ビジュアルは、Azure Data Explorer ダッシュボードの重要な部分です。 このドキュメントでは、さまざまなビジュアルの種類について詳しく説明し、ダッシュボードのユーザーがビジュアルをカスタマイズするために使用できるさまざまなオプションについて説明します。

> [!NOTE]
> ビジュアルのカスタマイズは、ダッシュボード エディターで編集モードの状態で行えます。

## <a name="prerequisites"></a>前提条件

[Azure Data Explorer ダッシュボードを使用してデータを視覚化する](azure-data-explorer-dashboards.md)

## <a name="types-of-visuals"></a>ビジュアルの種類

Azure Data Explorer では、さまざまな種類のビジュアルがサポートされています。 ここでは、さまざまな種類のビジュアルと、それらのビジュアルをプロットするために結果セットで必要なデータ列について説明します。

### <a name="table"></a>Table

:::image type="content" source="media/dashboard-customize-visuals/table.png" alt-text="テーブルのビジュアルの種類":::

既定で、結果はテーブルとして表示されます。 テーブルのビジュアルは、詳細または複雑なデータを表示する場合に最適です。

### <a name="bar-chart"></a>横棒グラフ

:::image type="content" source="media/dashboard-customize-visuals/bar-chart.png" alt-text="横棒グラフのビジュアルの種類":::

横棒グラフのビジュアルでは、クエリ結果に少なくと 2 つの列が必要です。 既定では、最初の列が y 軸として使用されます。 この列には、text、datetime、または numeric のデータ型を含めることができます。 その他の列は x 軸として使用され、横線として表示される numeric のデータ型が含まれます。 横棒グラフは、主に数値と不連続の公称値を比較するために使用されます。各線の長さはその値を表します。

### <a name="column-chart"></a>縦棒グラフ

:::image type="content" source="media/dashboard-customize-visuals/column-chart.png" alt-text="縦棒グラフのビジュアルの種類":::

縦棒グラフのビジュアルでは、クエリ結果に少なくと 2 つの列が必要です。 既定では、最初の列が x 軸として使用されます。 この列には、text、datetime、または numeric のデータ型を含めることができます。 その他の列は y 軸として使用され、縦線として表示される数値のデータ型が含まれます。 縦棒グラフは、メイン カテゴリ範囲内の特定のサブ カテゴリ項目を比較するために使用されます。各線の長さはその値を表します。

### <a name="area-chart"></a>面グラフ

:::image type="content" source="media/dashboard-customize-visuals/area-chart.png" alt-text="面グラフのビジュアルの種類":::

面グラフのビジュアルには、時系列のリレーションシップが表示されます。 クエリの最初の列は数値である必要があり、x 軸として使用されます。 その他の数値列は y 軸です。 折れ線グラフとは異なり、面グラフではまた、ボリュームを視覚的に表します。 面グラフは、さまざまなデータ セット間の変化を示すのに最適です。

### <a name="line-chart"></a>折れ線グラフ

:::image type="content" source="media/dashboard-customize-visuals/line-chart.png" alt-text="折れ線グラフのビジュアルの種類":::

折れ線グラフのビジュアルは、最も基本的な種類のグラフです。 クエリの最初の列は数値である必要があり、x 軸として使用されます。 その他の数値列は y 軸です。 折れ線グラフは、短いおよび長い期間にわたる変更を追跡します。 小さい変化が存在する場合、折れ線グラフは横棒グラフよりも有用です。

### <a name="stat"></a>stat

:::image type="content" source="media/dashboard-customize-visuals/stat.png" alt-text="stat のビジュアルの種類":::

stat のビジュアルは、1 つの要素のみを表示します。 出力に複数の列と行がある場合、stat は最初の列の最初の要素を表示します。 stat カードは、ダッシュボードで KPI を強調表示する場合に便利です。

### <a name="pie-chart"></a>円グラフ

:::image type="content" source="media/dashboard-customize-visuals/pie-chart.png" alt-text="円グラフのビジュアルの種類":::

円グラフのビジュアルでは、クエリ結果に少なくとも 2 つの列が必要です。 既定では、最初の列は色軸として使用されます。 この列には、text、datetime、または numeric のデータ型を含めることができます。 その他の列は、各スライスのサイズを決定するために使用され、数値のデータ型が含まれます。 円グラフは、カテゴリの構成と全体に占めるその割合を表すために使用されます。

### <a name="scatter-chart"></a>散布図

:::image type="content" source="media/dashboard-customize-visuals/scatter-chart.png" alt-text="散布図のビジュアルの種類":::

散布図のビジュアルでは、最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 散布図は、変数間のリレーションシップを観察するために使用されます。

### <a name="time-chart"></a>時間グラフ 

:::image type="content" source="media/dashboard-customize-visuals/time-chart.png" alt-text="時間グラフのビジュアルの種類":::

時間グラフのビジュアルは折れ線グラフの一種です。 クエリの最初の列は x 軸で、datetime である必要があります。 その他の数値列は y 軸です。 1 つの文字列の列の値を使用して数値列をグループ化し、グラフ内にさまざまな線を作成します。 他の文字列の列は無視されます。 時間グラフのビジュアルは[折れ線グラフ](#line-chart)に似ていますが、x 軸は常に時間になります。

### <a name="anomaly-chart"></a>異常グラフ 

:::image type="content" source="media/dashboard-customize-visuals/anomaly-chart.png" alt-text="異常グラフのビジュアルの種類":::

異常グラフのビジュアルは[時間グラフ](#time-chart)と似ていますが、`series_decompose_anomalies` 関数を使用して異常を強調表示します。

### <a name="map"></a>マップ

:::image type="content" source="media/dashboard-customize-visuals/map.png" alt-text="マップのビジュアルの種類":::

マップのビジュアルを表示するには、クエリに 4 つの列が必要です。
* ホバー ラベルに使用される文字列 (最初の列)
* 経度 (実数)
* 緯度 (実数)
* バブルのサイズ (整数)。 異なるサイズが必要でない場合は、この列を定数にすることができます。

マップは、地理座標を使用してデータを視覚化するのに役立ちます。 マップのビジュアルには、組み込みのズーム機能もあります。

## <a name="customize-visuals"></a>ビジュアルをカスタマイズする

1. ダッシュボード メニューの **[編集]** を選択して、編集モードに切り替えます。
1. カードのビジュアルのカスタマイズ ダイアログにアクセスするには、ドロップダウン メニュー > **[カードの編集]** をクリックします。 または、 **[クエリの追加]** を使用して新しいカードを作成するときに、 **[カードの編集]** を選択します。

:::image type="content" source="media/dashboard-customize-visuals/edit-card.png" alt-text="ビジュアルのカスタマイズでのカードの編集":::

### <a name="select-properties-to-customize-visuals"></a>ビジュアルをカスタマイズするためのプロパティを選択する

次のプロパティを使用して、ビジュアルをカスタマイズします。

:::image type="content" source="media/dashboard-customize-visuals/visual-customization-sidebar.png" alt-text="ビジュアルのカスタマイズ サイドバー":::

|セクション  |説明 | ビジュアルの種類
|---------|---------|-----|
|**全般**    |    **積み上げ**または**非積み上げ**グラフの形式を選択します  | 横棒、縦棒、面グラフ |
|**データ**    |   ビジュアルの **Y と X 列**を選択します。 クエリ結果に基づいてプラットフォームにより自動的に列が選択されるようにする場合は、選択内容を **[Infer]\(推測\)** のままにしておきます    |横棒、縦棒、散布図、異常グラフ|
|**凡例**    |   ビジュアルの凡例の表示を表示または非表示に切り替えます   |横棒、縦棒、面、折れ線、散布図、異常、時間グラフ |
|**Y 軸**     |   Y 軸のプロパティをカスタマイズできます。 <ul><li>**ラベル**:カスタム ラベルのテキスト。 </li><li>**最大値**: Y 軸の最大値を変更します。  </li><li>**最小値**: Y 軸の最小値を変更します。  </li></ul>      |横棒、縦棒、面、折れ線、散布図、異常、時間グラフ |
|**X 軸**     |    X 軸のプロパティをカスタマイズできます。 <ul><li>**ラベル**:カスタム ラベルのテキスト。 </li>     | 横棒、縦棒、面、折れ線、散布図、異常、時間グラフ|

## <a name="next-steps"></a>次のステップ

* [Azure Data Explorer ダッシュボードでパラメーターを使用する](dashboard-parameters.md)
* [Azure Data Explorer でデータのクエリを実行する](web-query-data.md) 