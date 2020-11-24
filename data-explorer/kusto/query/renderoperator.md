---
title: render 操作-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーのレンダー演算子について説明します。
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
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783337"
---
# <a name="render-operator"></a>render 演算子

ユーザーエージェントに対して、クエリの結果を特定の方法で表示するように指示します。

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * Render 操作は、クエリの最後の演算子である必要があります。また、1つの表形式のデータストリームの結果を生成するクエリでのみ使用されます。
> * Render 操作では、データは変更されません。 結果の拡張プロパティに注釈 ("視覚化") が挿入されます。 注釈には、クエリ内の演算子によって提供される情報が含まれています。
> * 視覚化情報の解釈は、ユーザーエージェントによって行われます。 さまざまなエージェント (Kusto. エクスプローラー、Kusto. WebExplorer など) では、さまざまな視覚エフェクトがサポートされる場合があります。

## <a name="syntax"></a>Syntax

*T* `|` `render` *視覚化* [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

各値の説明:

* *視覚化* は、使用する視覚エフェクトの種類を示します。 サポートされる値は

::: zone pivot="azuredataexplorer"

|*視覚化*     |説明|
|--------------------|-|
| `anomalychart`     | 線上と同様ですが、 [series_decompose_anomalies](./series-decompose-anomaliesfunction.md)関数を使用した[異常を強調](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)表示します。 |
| `areachart`        | 面グラフ。 最初の列は x 軸で、数値型の列である必要があります。 その他の数値列は y 軸です。 |
| `barchart`         | 最初の列は x 軸で、テキスト、datetime、または数値を指定できます。 その他の列は数値であり、横方向のストリップとして表示されます。|
| `card`             | 最初の結果レコードは、スカラー値のセットとして扱われ、カードとして表示されます。 |
| `columnchart`      | と似 `barchart` ています。|
| `ladderchart`      | 最後の2つの列は x 軸、他の列は y 軸です。|
| `linechart`        | 線グラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `piechart`         | 最初の列は色の軸、2 番目の列は数値です。 |
| `pivotchart`       | ピボットテーブルとグラフを表示します。 ユーザーは、データ、列、行、さまざまな種類のグラフを対話的に選択できます。 |
| `scatterchart`     | ポイントグラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `stackedareachart` | 積み上げ面グラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `table`            | 既定-結果はテーブルとして表示されます。|
| `timechart`        | 線グラフ。 最初の列は x 軸で、日時を指定する必要があります。 その他の (数値) 列は y 軸です。 数値列を "グループ化" してグラフ内に異なる線を作成するために使用される文字列型の列が1つあります (さらに文字列の列は無視されます)。 |
| `timepivot`        | イベントのタイムラインでの対話型ナビゲーション (時間軸でのピボット)|

::: zone-end

::: zone pivot="azuremonitor"

|*視覚化*     |説明|
|--------------------|-|
| `areachart`        | 面グラフ。 最初の列は x 軸で、数値型の列である必要があります。 その他の数値列は y 軸です。 |
| `barchart`         | 最初の列は x 軸で、テキスト、datetime、または数値を指定できます。 その他の列は数値であり、横方向のストリップとして表示されます。|
| `columnchart`      | と似 `barchart` ています。|
| `piechart`         | 最初の列は色の軸、2 番目の列は数値です。 |
| `scatterchart`     | ポイントグラフ。 最初の列は x 軸で、数値型の列である必要があります。 その他の数値列は y 軸です。 |
| `table`            | 既定-結果はテーブルとして表示されます。|
| `timechart`        | 線グラフ。 最初の列は x 軸で、日時を指定する必要があります。 その他の (数値) 列は y 軸です。 数値列を "グループ化" してグラフ内に異なる線を作成するために使用される文字列型の列が1つあります (さらに文字列の列は無視されます)。|

::: zone-end

* *PropertyName* /*PropertyValue* は、レンダリング時に使用する追加情報を示します。
  すべてのプロパティは省略可能です。 サポートされているプロパティは次のとおりです。

::: zone pivot="azuredataexplorer"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |各メジャーの値をそのすべての先行するに追加するかどうかを指定します。 ( `true` または `false` )|
|`kind`        |視覚エフェクトの種類をさらに改善。 次を参照してください。                         |
|`legend`      |凡例を表示するかどうか ( `visible` または) を指定 `hidden` します。                       |
|`series`      |レコードごとの値を結合してレコードが属する系列を定義する、コンマ区切りの列の一覧です。|
|`ymin`        |Y 軸に表示される最小値。                                      |
|`ymax`        |Y 軸に表示される最大値。                                      |
|`title`       |視覚化のタイトル (型 `string` )。                                |
|`xaxis`       |X 軸のスケールを設定する方法 ( `linear` または `log` )                                      |
|`xcolumn`     |X 軸では、結果の列が使用されます。                                |
|`xtitle`      |X 軸の (型の `string` ) タイトル。                                       |
|`yaxis`       |Y 軸のスケールを設定する方法 ( `linear` または `log` )                                      |
|`ycolumns`    |X 列の値ごとに指定された値で構成される列のコンマ区切りの一覧。|
|`ysplit`      |複数の視覚エフェクトを分割する方法。 次を参照してください。                               |
|`ytitle`      |Y 軸のタイトル (型 `string` )。                                       |
|`anomalycolumns`|にのみ関連するプロパティ `anomalychart` です。 異常系列と見なされ、グラフ上にポイントとして表示される列のコンマ区切りの一覧|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*PropertyValue*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |視覚エフェクトの種類をさらに改善。 次を参照してください。                         |
|`series`      |レコードごとの値を結合してレコードが属する系列を定義する、コンマ区切りの列の一覧です。|
|`title`       |視覚化のタイトル (型 `string` )。                                |
|`yaxis`       |Y 軸のスケールを設定する方法 ( `linear` または `log` )                                      |

::: zone-end

いくつかの視覚化は、プロパティを指定することによってさらに詳細にすることができ `kind` ます。
これらのボタンの役割は、次のとおりです。

|*視覚化*|`kind`             |説明                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |各 "区分" は独自のものです。     |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |"Areas" を右側に積み重ねます。        |
|               |`stacked100`       |"Areas" を右側に積み重ね、それぞれを他の文字と同じ幅に引き伸ばすようにします。|
|`barchart`     |`default`          |各 "bar" は独自のものです。      |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |"バー" をスタックします。                      |
|               |`stacked100`       |"Bard" をスタックし、それぞれを他と同じ幅に伸縮します。|
|`columnchart`  |`default`          |各 "列" は、それ自体を表します。   |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |"Columns" は他方の一番上にあります。|
|               |`stacked100`       |"Columns" をスタックし、それぞれを他と同じ高さに伸縮します。|
|`scatterchart` |`map`              |期待される列は、[経度、緯度] または GeoJSON ポイントです。 系列列は省略可能です。|
|`piechart`     |`map`              |期待される列は、[経度,、緯度] または GeoJSON ポイント、カラー軸、および数値です。 Kusto エクスプローラーデスクトップでサポートされています。|

::: zone pivot="azuredataexplorer"

いくつかの視覚化では、複数の y 軸値への分割がサポートされるようになります。

|`ysplit`  |説明                                                       |
|----------|------------------------------------------------------------------|
|`none`    |すべての系列データに対して1つの y 軸が表示されます。 (既定値)。       |
|`axes`    |1つのグラフが、複数の y 軸 (系列ごとに1つ) と共に表示されます。|
|`panels`  |値ごとに1つのグラフが表示され `ycolumn` ます (上限まで)。|

::: zone-end

> [!NOTE]
> Render 操作のデータモデルは、次の3種類の列があるかのように表形式のデータを参照します。
>
> * X 軸の列 (プロパティで示され `xcolumn` ます)。
> * 系列列 (プロパティによって示される任意の数の列 `series` )。各レコードについて、これらの列の結合された値によって1つの系列が定義されます。グラフの系列は、個別の結合値と同じになります。
> * Y 軸の列 (プロパティによって示される任意の数の列 `ycolumns` )。
  各レコードについて、系列には y 軸の列と同じ数の測定値 (グラフに "ポイント") があります。

> [!TIP]
> 
> * 、、およびを使用して、 `where` `summarize` 表示するボリュームを制限し `top` ます。
> * X 軸の順序を定義するためにデータを並べ替えます。
> * ユーザーエージェントは、クエリで指定されていないプロパティの値を自由に "推測" できます。 具体的には、結果のスキーマに "意味のない" 列があると、誤った推測に変換される可能性があります。 そのような場合は、そのような列を射影してみてください。 

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

[チュートリアルのレンダリング例](./tutorial.md#displaychartortable)

[異常検出](./samples.md#get-more-from-your-data-by-using-kusto-with-machine-learning)

::: zone-end
