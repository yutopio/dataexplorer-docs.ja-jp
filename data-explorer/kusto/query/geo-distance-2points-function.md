---
title: geo_distance_2points() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの geo_distance_2points() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 4b63ffaee8123b4ee281426f30f757fbe5165e75
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663701"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

地球上の 2 つの地理空間座標間の最短距離を計算します。

**構文**

`geo_distance_2points(`*p1_longitude*`, `*p1_latitude*`, ``, `*p2_latitude* *p1_longitudep1_latitudep2_longitudep2_latitude*`)`

**引数**

* *p1_longitude*: 最初の地理空間座標、経度の値 (度)。 有効な値は実数で、範囲 [-180, +180] です。
* *p1_latitude*: 最初の地理空間座標、緯度値(度) 有効な値は実数で、範囲 [-90, +90] です。
* *p2_longitude*: 2 番目の地理空間座標、経度の値 (度) 有効な値は実数で、範囲 [-180, +180] です。
* *p2_latitude*: 2 番目の地理空間座標、緯度値 (度)。 有効な値は実数で、範囲 [-90, +90] です。

**戻り値**

地球上の 2 つの地理的位置の間の最短距離 (メートル単位)。 座標が無効な場合、クエリは null の結果を生成します。

> [!NOTE]
> * 地理空間座標は[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標参照系によって表される座標として解釈されます。
> * 地球上の距離を測定するために使用される[測地データム](https://en.wikipedia.org/wiki/Geodetic_datum)は球体です。

**使用例**

次の例では、シアトルとロサンゼルス間の最短距離を検索します。

:::image type="content" source="images/queries/geo/distance_2points_seattle_los_angeles.png" alt-text="シアトルとロサンゼルス間の距離":::

```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

ここでは、シアトルからロンドンまでの最短パスの概算です。 線はラインストリングに沿って、そしてそれから500メートル以内の座標から成ります。

:::image type="content" source="images/queries/geo/line_seattle_london.png" alt-text="シアトルからロンドンへのラインストリング":::

```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

次の例では、2 つの座標間の最短距離が 1 ~ 11 メートルの間にあるすべての行を検索します。
```kusto
StormEvents
| extend distance_1_to_11m = geo_distance_2points(BeginLon, BeginLat, EndLon, EndLat)
| where distance_1_to_11m between (1 .. 11)
| project distance_1_to_11m
```

| distance_1_to_11m |
|-------------------|
| 10.5723100154958  |
| 7.92153588248414  |

次の例では、無効な座標入力のため、null の結果を返します。
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |