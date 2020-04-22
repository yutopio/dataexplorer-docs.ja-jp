---
title: レンダー オペレータ - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでのレンダリング オペレーターについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/29/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 645860405a165f3304f655e42d2ced9b8777b8d0
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765477"
---
# <a name="render-operator"></a>render 演算子

特定の方法でクエリの結果を表示するようにユーザー エージェントに指示します。

```kusto
range x from 0.0 to 2*pi() step 0.01 | extend y=sin(x) | render linechart
```

> [!NOTE]
> * render 演算子は、クエリの最後の演算子で、単一の表形式のデータ ストリームの結果を生成するクエリでのみ使用する必要があります。
> * レンダー オペレータはデータを変更しません。 結果の拡張プロパティに注釈 (「視覚化」) を挿入します。 アノテーションには、クエリで演算子によって提供される情報が含まれます。
> * 視覚化情報の解釈は、ユーザーエージェントによって行われます。 さまざまなエージェント (Kusto.Explorer、Kusto.WebExplorer など) は、さまざまな視覚化をサポートする場合があります。

**構文**

*T*`|``with``(`*PropertyName*`=`*PropertyValue*`,`*Visualization*ビジュアライゼーション [ プロパティ名 プロパティ値 ] `render``)`]

各値の説明:

* *ビジュアライゼーション*は、使用するビジュアライゼーションの種類を示します。 サポートされる値は

::: zone pivot="azuredataexplorer"

|*可視 化*     |説明|
|--------------------|-|
| `anomalychart`     | タイムチャートと似ていますが、[関数](./series-decompose-anomaliesfunction.md)を使用して異常series_decompose_anomalies[強調表示](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)します。 |
| `areachart`        | エリアグラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `barchart`         | 最初の列は x 軸で、テキスト、日時、数値を指定できます。 その他の列は数値で、水平ストリップとして表示されます。|
| `card`             | 最初の結果レコードはスカラー値のセットとして扱われ、カードとして表示されます。 |
| `columnchart`      | 水平`barchart`ストリップの代わりに垂直ストリップと同様に。|
| `ladderchart`      | 最後の 2 つの列は x 軸で、他の列は y 軸です。|
| `linechart`        | 線グラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `piechart`         | 最初の列は色の軸、2 番目の列は数値です。 |
| `pivotchart`       | ピボット テーブルとグラフを表示します。 ユーザーは、対話的にデータ、列、行、およびさまざまなグラフの種類を選択できます。 |
| `scatterchart`     | ポイントグラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `stackedareachart` | 積み上げ面グラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `table`            | デフォルト - 結果は表として表示されます。|
| `timechart`        | 線グラフ。 最初の列は x 軸で、日時を指定する必要があります。 その他の (数値) 列は y 軸です。 数値列を "グループ化" し、グラフ内に異なる線を作成するために使用される文字列列が 1 つあります (文字列列は無視されます)。 |
| `timepivot`        | イベントの時系列上のインタラクティブなナビゲーション (時間軸に基づいてピボット)|

::: zone-end

::: zone pivot="azuremonitor"

|*可視 化*     |説明|
|--------------------|-|
| `areachart`        | エリアグラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `barchart`         | 最初の列は x 軸で、テキスト、日時、数値を指定できます。 その他の列は数値で、水平ストリップとして表示されます。|
| `columnchart`      | 水平`barchart`ストリップの代わりに垂直ストリップと同様に。|
| `piechart`         | 最初の列は色の軸、2 番目の列は数値です。 |
| `scatterchart`     | ポイントグラフ。 最初の列は x 軸で、数値列である必要があります。 その他の数値列は y 軸です。 |
| `table`            | デフォルト - 結果は表として表示されます。|
| `timechart`        | 線グラフ。 最初の列は x 軸で、日時を指定する必要があります。 その他の (数値) 列は y 軸です。 数値列を "グループ化" し、グラフ内に異なる線を作成するために使用される文字列列が 1 つあります (文字列列は無視されます)。|

::: zone-end

* *プロパティ名*/*プロパティ値*は、レンダリング時に使用する追加情報を示します。
  すべてのプロパティはオプションです。 サポートされているプロパティは次のとおりです。

::: zone pivot="azuredataexplorer"

|*PropertyName*|*プロパティ値*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`accumulate`  |各メジャーの値が、その先行するすべてのメジャーに追加されるかどうか。 (`true` `false`または )|
|`kind`        |可視化の種類のさらなる精緻化。 以下を参照してください。                         |
|`legend`      |凡例を表示するかどうか (`visible`または`hidden`) を表示しない                       |
|`series`      |レコードごとの値を結合してレコードが属する系列を定義する列のコンマ区切りリスト。|
|`ymin`        |Y 軸に表示される最小値。                                      |
|`ymax`        |Y 軸に表示される最大値。                                      |
|`title`       |ビジュアライゼーションのタイトル`string`( タイプ )                                |
|`xaxis`       |X 軸 (または`linear``log`) のスケールを調整する方法。                                      |
|`xcolumn`     |結果のどの列が X 軸に使用されます。                                |
|`xtitle`      |X 軸のタイトル (タイプ`string`) です。                                       |
|`yaxis`       |y 軸 (または`linear``log`) のスケールを調整する方法。                                      |
|`ycolumns`    |x 列の値ごとに指定された値で構成される列のコンマ区切りリスト。|
|`ysplit`      |ビジュアライゼーションを複数分割する方法。 以下を参照してください。                               |
|`ytitle`      |Y 軸のタイトル (タイプ`string`) です。                                       |
|`anomalycolumns`|に対してのみ関連`anomalychart`するプロパティです。 コンマで区切られた列のリスト (異常系列と見なされ、チャート上のポイントとして表示されます)|

::: zone-end

::: zone pivot="azuremonitor"

|*PropertyName*|*プロパティ値*                                                                   |
|--------------|----------------------------------------------------------------------------------|
|`kind`        |可視化の種類のさらなる精緻化。 以下を参照してください。                         |
|`series`      |レコードごとの値を結合してレコードが属する系列を定義する列のコンマ区切りリスト。|
|`title`       |ビジュアライゼーションのタイトル`string`( タイプ )                                |
|`yaxis`       |y 軸 (または`linear``log`) のスケールを調整する方法。                                      |

::: zone-end

一部の視覚化は、プロパティを提供することによってさらに`kind`詳しく説明できます。
これらのボタンの役割は、次のとおりです。

|*可視 化*|`kind`             |説明                        |
|---------------|-------------------|-----------------------------------|
|`areachart`    |`default`          |各「エリア」は単独で立っています。     |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |右側に「エリア」を積み重ね        |
|               |`stacked100`       |「領域」を右に積み重ね、それぞれを他の領域と同じ幅に伸ばします。|
|`barchart`     |`default`          |各「バー」は単独で立っています。      |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |スタック"バー"。                      |
|               |`stacked100`       |"bard" を積み重ね、それぞれを他の幅と同じ幅に伸ばします。|
|`columnchart`  |`default`          |各「列」は単独で立っています。   |
|               |`unstacked`        |`default` と同じ。                 |
|               |`stacked`          |「列」をもう一方の上に積み重ねる。|
|               |`stacked100`       |"列" を積み重ね、それぞれを他の列と同じ高さに伸ばします。|
|`piechart`     |`map`              |期待される列は[経度、緯度]または GeoJSON ポイント、色軸、数値です。 Kusto エクスプローラ デスクトップでサポートされています。|
|`scatterchart` |`map`              |期待される列は[経度、緯度]または GeoJSON ポイントです。 系列列はオプションです。 Kusto エクスプローラ デスクトップでサポートされています。|

::: zone pivot="azuredataexplorer"

一部のビジュアライゼーションでは、複数の Y 軸値への分割がサポートされています。

|`ysplit`  |説明                                                       |
|----------|------------------------------------------------------------------|
|`none`    |すべての系列データに対して単一の Y 軸が表示されます。 (既定値)。       |
|`axes`    |1 つのグラフには、複数の Y 軸 (系列ごとに 1 つ) が表示されます。|
|`panels`  |1 つのグラフが値`ycolumn`ごとにレンダリングされます (ある制限まで)。|

::: zone-end

> [!NOTE]
> レンダー オペレータのデータ モデルは、表形式のデータを、次の 3 種類の列を持っているかのように見えます。
>
> * x 軸の列 (プロパティ`xcolumn`で示されます)。
> * 系列の列 (`series`プロパティによって示される列の任意の数)。各レコードについて、これらの列の合計値は 1 つの系列を定義し、グラフには、個別の結合値があるのと同じ数の系列があります。
> * y 軸の列 (プロパティで示される任意`ycolumns`の数の列)。
  各レコードの系列には、Y 軸の列と同じ数の測定値 (グラフ内の "ポイント" ) があります。

> [!TIP]
> 
> * を`where``summarize`使用し`top`、表示するボリュームを制限します。
> * データを並べ替え、x 軸の順序を定義します。
> * ユーザー エージェントは、クエリで指定されていないプロパティの値を自由に推測できます。 特に、結果のスキーマに 「面白くない」列があると、推測が間違っていることにつながる可能性があります。 そのような列が発生したら、投影してみてください。 

**例**

```kusto
range x from -2 to 2 step 0.1
| extend sin = sin(x), cos = cos(x)
| extend x_sign = iif(x > 0, "x_pos", "x_neg")
| extend sum_sign = iif(sin + cos > 0, "sum_pos", "sum_neg")
| render linechart with  (ycolumns = sin, cos, series = x_sign, sum_sign)
```

::: zone pivot="azuredataexplorer"

[チュートリアルのレンダリング例](./tutorial.md#render-display-a-chart-or-table).

[異常検出](./samples.md#get-more-out-of-your-data-in-kusto-using-machine-learning)

::: zone-end
