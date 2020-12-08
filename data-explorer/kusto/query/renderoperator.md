---
title: render 演算子 - Azure Data Explorer
description: この記事では、Azure Data Explorer の render 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5670f3f9c7aa8b3d6b10f88433d19246e2daf6d6
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95783337"
---
# <a name="render-operator"></a>render 演算子

ユーザー エージェントに、クエリの結果を特定の方法でレンダリングするように指示します。

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * render 演算子は、クエリの最後の演算子として指定し、1 つの表形式のデータ ストリームの結果が生成されるクエリでのみ使用する必要があります。
> * render 演算子によってデータが変更されることはありません。 結果の拡張プロパティに注釈 ("Visualization") が挿入されます。 注釈には、クエリ内の演算子によって提供される情報が含まれます。
> * 視覚化情報の解釈は、ユーザー エージェントによって行われます。 エージェントが異なると (Kusto.Explorer、Kusto.WebExplorer など)、サポートされる視覚化が異なる場合があります。

## <a name="syntax"></a>構文

*T* `|` `render` *Visualization* [`with` `(` *PropertyName* `=` *PropertyValue* [`,` ...] `)`]

この場合、

* *Visualization* は、使用される視覚化の種類を示します。 サポートされる値は

::: zone pivot="azuredataexplorer"

|*視覚化*     |[説明]|
|--------------------|-|
| `anomalychart`     | timechart に似ていますが、[series_decompose_anomalies](./series-decompose-anomaliesfunction.md) 関数を使用して[異常が強調表示](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)されます。 |
| `areachart`        | 面グラフ。 最初の列は x 軸であり、数値列である必要があります。 その他の数値列は y 軸です。 |
| `barchart`         | 最初の列は x 軸であり、テキスト、日時、または数値を使用できます。 他の列は数値であり、横方向のストリップとして表示されます。|
| `card`             | 最初の結果レコードは、スカラー値のセットとして扱われ、カードとして表示されます。 |
| `columnchart`      | `barchart` と同様ですが、横方向のストリップではなく、縦方向のストリップになります。|
| `ladderchart`      | 最後の 2 つの列は x 軸であり、他の列は y 軸です。|
| `linechart`        | 線グラフ。 最初の列は x 軸であり、数値列である必要があります。 その他の数値列は y 軸です。 |
| `piechart`         | 最初の列は色の軸、2 番目の列は数値です。 |
| `pivotchart`       | ピボット テーブルとグラフが表示されます。 ユーザーは、データ、列、行、さまざまなグラフの種類を、対話的に選択できます。 |
| `scatterchart`     | 点グラフ。 最初の列は x 軸であり、数値列である必要があります。 その他の数値列は y 軸です。 |
| `stackedareachart` | 積み上げ面グラフ。 最初の列は x 軸であり、数値列である必要があります。 その他の数値列は y 軸です。 |
| `table`            | 既定 - 結果はテーブルとして表示されます。|
| `timechart`        | 線グラフ。 最初の列は x 軸で、日時を指定する必要があります。 他の (数値) 列は y 軸です。 1 つの文字列の列の値が、数値列を "グループ化" し、グラフ内の異なる線を作成するために使用されます (それ以外の文字列の列は無視されます)。 |
| `timepivot`        | イベントのタイムラインでの対話型ナビゲーション (時間軸でのピボット)|

::: zone-end

::: zone pivot="azuremonitor"

|*視覚化*     |[説明]|
|--------------------|-|
| `areachart`        | 面グラフ。 最初の列は x 軸であり、数値列である必要があります。 その他の数値列は y 軸です。 |
| `barchart`         | 最初の列は x 軸であり、テキスト、日時、または数値を使用できます。 他の列は数値であり、横方向のストリップとして表示されます。|
| `columnchart`      | `barchart` と同様ですが、横方向のストリップではなく、縦方向のストリップになります。|
| `piechart`         | 最初の列は色の軸、2 番目の列は数値です。 |
| `scatterchart`     | 点グラフ。 最初の列は x 軸であり、数値列である必要があります。 その他の数値列は y 軸です。 |
| `table`            | 既定 - 結果はテーブルとして表示されます。|
| `timechart`        | 線グラフ。 最初の列は x 軸で、日時を指定する必要があります。 他の (数値) 列は y 軸です。 1 つの文字列の列の値が、数値列を "グループ化" し、グラフ内の異なる線を作成するために使用されます (それ以外の文字列の列は無視されます)。|

::: zone-end

* *PropertyName*/*PropertyValue* は、レンダリング時に使用される追加情報を示します。
  すべてのプロパティは省略可能です。 サポートされているプロパティは次のとおりです。

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |各メジャーの値を、それより前にあるすべてのものに追加するかどうか。 (`true` または `false`)|
|`kind`        |視覚化の種類のさらに詳細な設定。 次を参照してください。                         |
|`legend`      |凡例を表示するかどうか (`visible` または `hidden`)。                       |
|`series`      |レコードごとに結合された値によってそのレコードが属する系列が定義される、コンマ区切りの列のリスト。|
|`ymin`        |Y 軸に表示される最小値。                                      |
|`ymax`        |Y 軸に表示される最大値。                                      |
|`title`       |視覚化のタイトル (`string` 型)。                                |
|`xaxis`       |x 軸のスケールを設定する方法 (`linear` または `log`)。                                      |
|`xcolumn`     |x 軸に使用される結果の列。                                |
|`xtitle`      |x 軸のタイトル (`string` 型)。                                       |
|`yaxis`       |y 軸のスケールを設定する方法 (`linear` または `log`)。                                      |
|`ycolumns`    |x 列の値ごとに提供された値で構成される列のコンマ区切りのリスト。|
|`ysplit`      |複数の視覚化を分割する方法。 次を参照してください。                               |
|`ytitle`      |y 軸のタイトル (`string` 型)。                                       |
|`anomalycolumns`|`anomalychart` にのみ関連するプロパティ。 異常系列と見なされ、グラフ上に点として表示される列のコンマ区切りのリスト|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |視覚化の種類のさらに詳細な設定。 次を参照してください。                         |
|`series`      |レコードごとに結合された値によってそのレコードが属する系列が定義される、コンマ区切りの列のリスト。|
|`title`       |視覚化のタイトル (`string` 型)。                                |
|`yaxis`       |y 軸のスケールを設定する方法 (`linear` または `log`)。                                      |

::: zone-end

一部の視覚化は、`kind` プロパティを指定することにより、さらに詳細に設定することができます。
これらのボタンの役割は、次のとおりです。

|*視覚化*|`kind`             |[説明]                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |各 "面" は独立しています。     |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |"面" を右側に積み上げます。        |
|               |`stacked100`       |"面" を右側に積み上げ、それぞれを他と同じ幅に伸縮します。|
|`barchart`     |`default`          |各 "横棒" は独立しています。      |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |"横棒" を積み上げます。                      |
|               |`stacked100`       |"横棒" を積み上げて、それぞれを他と同じ幅に伸縮します。|
|`columnchart`  |`default`          |各 "縦棒" は独立しています。   |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |"縦棒" を他の上に積み上げます。|
|               |`stacked100`       |"縦棒" を積み上げて、それぞれを他と同じ高さに伸縮します。|
|`scatterchart` |`map`              |予期される列は、[経度、緯度] または GeoJSON ポイントです。 系列の列は省略可能です。|
|`piechart`     |`map`              |予期される列は、[経度、緯度]、GeoJSON ポイント、カラー軸、および数値です。 Kusto Explorer デスクトップでサポートされます。|

::: zone pivot="azuredataexplorer"

一部の視覚化では、複数の y 軸値への分割がサポートされています。

|`ysplit`  |[説明]                                                       |
|----------|------------------------------------------------------------------|
|`none`    |1 つの y 軸がすべての系列データに表示されます。 (既定値)。       |
|`axes`    |1 つのグラフに、複数の y 軸 (系列ごとに 1 つ) が表示されます。|
|`panels`  |`ycolumn` の値ごとに 1 つのグラフが表示されます (何らかの上限まで)。|

::: zone-end

> [!NOTE]
> render 演算子のデータ モデルでは、表形式のデータは 3 種類の列があるように認識されます。
>
> * x 軸の列 (`xcolumn` プロパティによって示されます)。
> * 系列の列 (`series` プロパティによって示される任意の数の列)。レコードごとに、これらの列の結合された値によって 1 つの系列が定義され、グラフの系列の数は、結合された個別の値と同じになります。
> * y 軸の列 (`ycolumns` プロパティによって示される任意の数の列)。
  レコードごとに、系列には y 軸の列と同じ数の測定値 (グラフ内の "ポイント") があります。

> [!TIP]
> 
> * 表示する量を制限するには、`where`、`summarize`、`top` を使用します。
> * x 軸の順序を定義するには、データを並べ替えます。
> * クエリによって指定されていないプロパティの値は、ユーザー エージェントで自由に "推測" できます。 具体的には、結果のスキーマに "興味のない" 列があると、誤った推測に変換される可能性があります。 その場合は、そのような列に project-away を使用してみてください。 

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[チュートリアルでのレンダリングの例](./tutorial.md#displaychartortable)

[Anomaly detection](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning) (異常検出)

::: zone-end
