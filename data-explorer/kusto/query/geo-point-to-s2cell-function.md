---
title: geo_point_to_s2cell() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでgeo_point_to_s2cell() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 253b850da519aceb0ead9456f7e49d6dd37d78ec
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663732"
---
# <a name="geo_point_to_s2cell"></a>geo_point_to_s2cell()

地理的な場所の S2 セル トークン文字列値を計算します。

S2 セルの詳細については、[ここを](http://s2geometry.io/devguide/s2cell_hierarchy)クリックしてください。

**構文**

`geo_point_to_s2cell(`*経度*`, `*の緯度*`, `*レベル*`)`

**引数**

* *経度*: 地理的位置の経度の値。 x が実数で、x が範囲 [-180, +180] の場合、経度 x は有効と見なされます。 
* *緯度*: 地理的位置の緯度値。 y が実数で y が範囲 [-90, +90] の場合、緯度 y は有効と見なされます。 
* *level*:`int`要求されたセル レベルを定義する省略可能です。 サポートされている値は [0,30] の範囲です。 指定されていない場合は、既定値`11`が使用されます。

**戻り値**

指定された地理的位置の S2 セル トークン文字列値。 座標またはレベルが無効な場合、クエリは空の結果を生成します。

> [!NOTE]
>
> * S2Cell は、地理空間クラスタリングツールとして便利です。
> * S2Cellは、最も低いレベルのレベル0から00.44cm²の85,011,012.19km²の範囲の面積カバレッジを持つ31レベルの階層を持っています。
> * S2Cell は、レベルが 0 から 30 に増加した場合でもセルの中心を十分に保持します。
> * S2Cell は球面上のセルで、エッジは測地線です。
> * 経度 x と緯度 y で計算された s2cell トークン文字列に対して[geo_s2cell_to_central_point()](geo-s2cell-to-central-point-function.md)関数を呼び出しても、必ずしも x と y が返されるとは限りません。
> * 2 つの地理的な場所が互いに非常に近いが、異なる S2 Cell トークンを持っている可能性があります。

**S2 レベル値あたりのセル概算エリア カバレッジ**

どのレベルでも、s2cell のサイズは同じですが、正確には同じではありません。 近くのセルのサイズはより等しくなる傾向があります。

|Level|最小ランダム セル エッジ長 (英国)|ランダム セルエッジの最大長 (US)|
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
|13|850メートル|1225メートル|
|14|425メートル|613メートル|
|15|212メートル|306メートル|
|16|106メートル|153メートル|
|17|53m|77m|
|18|27m|38m|
|19|13m|19m|
|20|7メートル|10メートル|
|21|3m|5メートル|
|22|166 cm|2m|
|23|83 cm|120cm|
|24|41 cm|60 cm|
|25|21cm|30cm|
|26|10cm|15cm|
|27|5cm|7 cm|
|28|2cm|4cm|
|29|12 mm|18 mm|
|30|6 mm|9 mm|

テーブルソースはこちらからご[覧いただけます](http://s2geometry.io/resources/s2cell_statistics)。

[geo_point_to_geohash()](geo-point-to-geohash-function.md)も参照してください。

**使用例**

s2cell によって集計された米国の嵐イベント。

:::image type="content" source="images/queries/geo/s2cell.png" alt-text="米国 s2 セル":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_s2cell(BeginLon, BeginLat, 5)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print s2cell = geo_point_to_s2cell(-80.195829, 25.802215, 8)
```

| s2セル |
|--------|
| 88d9b  |

次の例では、座標のグループを検索します。 グループ内のすべての座標のペアは、1632.45 km² の最大面積を持つ s2cell に存在します。
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

| s2セル | count | locations |
|--------|-------|-----------|
| 47b1d  | 2     | ["A"""B"] |
| 47ae3  | 1     | ["C"]     |

次の例では、無効な座標入力が原因で空の結果が生成されます。
```kusto
print s2cell = geo_point_to_s2cell(300,1,8)
```

| s2セル |
|--------|
|        |

次の例では、無効なレベル入力が原因で空の結果が生成されます。
```kusto
print s2cell = geo_point_to_s2cell(1,1,35)
```

| s2セル |
|--------|
|        |

次の例では、無効なレベル入力が原因で空の結果が生成されます。
```kusto
print s2cell = geo_point_to_s2cell(1,1,int(null))
```

| s2セル |
|--------|
|        |