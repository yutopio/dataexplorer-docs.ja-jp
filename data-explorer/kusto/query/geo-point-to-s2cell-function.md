---
title: geo_point_to_s2cell ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_point_to_s2cell () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: fe3af4218fcc8b714cd4d62e45e78d6f8c9c0270
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226909"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

地理的な場所の S2 セルトークン文字列値を計算します。

[S2 セル階層](https://s2geometry.io/devguide/s2cell_hierarchy)の詳細については、こちらをご覧ください。

**構文**

`geo_point_to_s2cell(`*経度* `, `*緯度* `, `*レベル*`)`

**引数**

* *経度*: 地理的な場所の経度の値。 *X*が実数で、 *x*が [-180, + 180] の範囲内にある場合、経度*x*は有効と見なされます。 
* *緯度*: 地理的な場所の緯度の値。 [-90, + 90] の範囲の y が実数と y の場合、緯度 y は有効と見なされます。 
* *level*: `int` 要求されたセルレベルを定義する省略可能な。 サポートされている値の範囲は [0, 30] です。 指定しない場合は、既定値 `11` が使用されます。

**戻り値**

指定された地理的な場所の S2 セルトークン文字列値。 座標またはレベルが無効である場合は、クエリによって空の結果が生成されます。

> [!NOTE]
>
> * S2 セルは、便利な地理空間クラスタリングツールにすることができます。
> * S2 セルには、範囲が31レベルの階層があります。これは、最上位レベルの0から 00.44 cm ²の最下位レベル30で、85011、012.19 km ²から cm ²までの範囲にわたります。
> * S2 セルは、レベルを0から30に増やしたときにセルの中心を維持します。
> * S2 セルは球表面のセルで、エッジは geodesics です。
> * 経度 x と緯度 y で計算された S2 セルトークン文字列に対して[geo_s2cell_to_central_point ()](geo-s2cell-to-central-point-function.md)関数を呼び出すと、必ずしも x と y が返されるとは限りません。
> * 2つの地理的位置が互いに近接していても、S2 セルトークンが異なる可能性があります。

**S2 セルのおおよその領域カバレッジのレベル値**

各レベルでは、S2 セルのサイズは似ていますが、まったく同じではありません。 隣接するセルのサイズが等しい傾向があります。

|Level|最小ランダムセルのエッジ長 (UK)|最大ランダムセル長の長さ (US)|
|--|--|--|
|0|7842 km|7842 km|
|1|3921 km|5004 km|
|2|1825 km|2489 km|
|3|840 km|1310 km|
|4|432 km|636 km|
|5|210 km|315 km|
|6|108 km|156 km|
|7|54 km|78 km|
|8|27 km|39 km|
|9|14 km|20 km|
|10|7 km|10 km|
|11|3 km|5 km|
|12|1699 m|2 km|
|13|850 m|1225 m|
|14|425 m|613 m|
|15|212 m|306 m|
|16|106 m|153 m|
|17|53 m|77 m|
|18|27 m|38 m|
|19|13 m|19 m|
|20|7 m|10 m|
|21|3 m|5 m|
|22|166 cm|2 m|
|23|83 cm|120 cm|
|24|41 cm|60 cm|
|25|21 cm|30 cm|
|26|10 cm|15 cm|
|27|5 cm|7 cm|
|28|2 cm|4 cm|
|29|12 mm|18 mm|
|30|6 mm|9 mm|

[この S2 セル統計リソースに](https://s2geometry.io/resources/s2cell_statistics)は、テーブルソースがあります。

「 [Geo_point_to_geohash ()](geo-point-to-geohash-function.md)」も参照してください。

**使用例**

S2cell によって集計された米国の嵐イベント。

:::image type="content" source="images/geo-point-to-s2cell-function/s2cell.png" alt-text="米国 s2cell":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2cell |
|--------|
| 88d9b  |

次の例では、座標のグループを検索します。 グループ内の座標の各ペアは、最大領域が 1632.45 km²の S2 セルに存在します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", 10.1234, 53,
  "B", 10.3579, 53,
  "C", 10.6842, 53,
]
| summarize count = count(),                                        // items per group count
            locations = make_list(location_id)                      // items in the group
            by s2cell = geo_point_to_s2cell(longitude, latitude, 8) // s2 cell of the group
```

| s2cell | count | locations |
|--------|-------|-----------|
| 47 b1d  | 2     | ["A", "B"] |
| 47ae3  | 1     | ["C"]     |

次の例では、座標入力が無効なため、空の結果が生成されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2cell |
|--------|
|        |

次の例では、無効なレベルの入力のため、空の結果が生成されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2cell |
|--------|
|        |

次の例では、無効なレベルの入力のため、空の結果が生成されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2cell |
|--------|
|        |
