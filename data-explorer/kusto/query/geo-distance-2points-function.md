---
title: geo_distance_2points ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_distance_2points () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 0e8d57e76b0dfa45003f541b54360cb4ac414646
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347886"
---
# <a name="geo_distance_2points"></a>geo_distance_2points()

地球上の2つの地理空間座標間の最短距離を計算します。

## <a name="syntax"></a>構文

`geo_distance_2points(`*p1_longitude* `, `*p1_latitude* `, `*p2_longitude* `, `*p2_latitude*`)`

## <a name="arguments"></a>引数

* *p1_longitude*: 最初の地理空間座標、経度の値 (度単位)。 有効な値は、実数と範囲 [-180, + 180] です。
* *p1_latitude*: 最初の地理空間座標、緯度の値 (度単位)。 有効な値は、実数と範囲 [-90, + 90] です。
* *p2_longitude*: 2 番目の地理空間座標、経度の値 (度単位)。 有効な値は、実数と範囲 [-180, + 180] です。
* *p2_latitude*: 2 番目の地理空間座標、緯度の値 (度)。 有効な値は、実数と範囲 [-90, + 90] です。

## <a name="returns"></a>戻り値

地球の2つの地理的位置間の最短距離 (メートル単位)。 座標が無効な場合は、クエリによって null の結果が生成されます。

> [!NOTE]
> * 地理空間座標は、 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標参照システムによって表されるものとして解釈されます。
> * 地球上の距離を測定するために使用される[測地 datum](https://en.wikipedia.org/wiki/Geodetic_datum)は球です。

## <a name="examples"></a>例

次の例では、シアトルとロサンゼルスの間の最短距離を検索します。

:::image type="content" source="images/geo-distance-2points-function/distance_2points_seattle_los_angeles.png" alt-text="シアトルとロサンゼルスの間の距離":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_2points(-122.407628, 47.578557, -118.275287, 34.019056)
```

| distance_in_meters |
|--------------------|
| 1546754.35197381   |

ここでは、シアトルからロンドンへの最短のパスを示します。 この線は、LineString に沿った座標と、それから500メートル以内の範囲で構成されます。

:::image type="content" source="images/geo-distance-2points-function/line_seattle_london.png" alt-text="シアトルからロンドンへの LineString":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range i from 1 to 1000000 step 1
| project lng = rand() * real(-122), lat = rand() * 90
| where lng between(real(-122) .. 0) and lat between(47 .. 90)
| where geo_distance_point_to_line(lng,lat,dynamic({"type":"LineString","coordinates":[[-122,47],[0,51]]})) < 500
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

次の例では、2つの座標間の最短距離が 1 ~ 11 メートルの範囲内にあるすべての行を検索します。

<!-- csl: https://help.kusto.windows.net/Samples -->
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

次の例では、座標入力が無効であるため、null 結果が返されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance = geo_distance_2points(300,1,1,1)
```

| distance |
|----------|
|          |
