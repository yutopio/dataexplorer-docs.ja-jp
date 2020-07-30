---
title: geo_distance_point_to_line ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_distance_point_to_line () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: a7796c14098f773b73bd16735a3d2c9c879c8fd2
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347869"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

地球の座標と線の間の最短距離を計算します。

## <a name="syntax"></a>構文

`geo_distance_point_to_line(`*経度* `, `*緯度* `, `*lineString*`)`

## <a name="arguments"></a>引数

* *経度*: 地理空間座標経度の値 (度単位)。 有効な値は、実数と範囲 [-180, + 180] です。
* *緯度*: 地理空間座標緯度の値 (度単位)。 有効な値は、実数と範囲 [-90, + 90] です。
* *lineString*: [GeoJSON 形式](https://tools.ietf.org/html/rfc7946)の行と[動的](./scalar-data-types/dynamic.md)データ型。

## <a name="returns"></a>戻り値

地球の座標と線の間の最短距離 (メートル単位)。 座標または lineString が無効な場合は、クエリによって null の結果が生成されます。

> [!NOTE]
> * 地理空間座標は、 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標参照システムによって表されるものとして解釈されます。
> * 地球上の距離を測定するために使用される[測地 datum](https://en.wikipedia.org/wiki/Geodetic_datum)は球です。 線の端は、球体の[geodesics](https://en.wikipedia.org/wiki/Geodesic)です。
> * 入力行のエッジが直交直線の場合は、平面エッジを geodesics に変換するために[geo_line_densify ()](geo-line-densify-function.md)を使用することを検討してください。

**LineString の定義と制約**

動的 ({"type": "LineString", "座標": [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

* LineString 座標配列には、少なくとも2つのエントリが含まれている必要があります。
* 座標 [経度,、緯度] は有効でなければなりません。経度は範囲 [-180, + 180] の実数で、緯度は範囲 [-90, + 90] の実数です。
* エッジの長さは180度未満でなければなりません。 2つの頂点の間の最短のエッジが選択されます。

> [!TIP]
> パフォーマンスを向上させるには、リテラル行を使用します。

## <a name="examples"></a>例

次の例では、北ラスベガス空港と近くの道路との最短距離を検索します。

:::image type="content" source="images/geo-distance-point-to-line-function/distance-point-to-line.png" alt-text="北ラスベガス空港と道路間の距離":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

東南海岸の嵐イベント。 イベントは、定義された行から最大 5 km までの距離によってフィルター処理されます。

:::image type="content" source="images/geo-distance-point-to-line-function/us-south-coast-storm-events.png" alt-text="米国南部海岸の嵐イベント":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

NY タクシーは、ups を集荷します。 ピックアップは、定義された行から 0.1 m までの最大距離によってフィルター処理されます。

:::image type="content" source="images/geo-distance-point-to-line-function/park-ave-ny-road.png" alt-text="米国中南部の嵐イベント":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

次の例では、LineString 入力が無効なため、null 結果が返されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

次の例では、座標入力が無効なため、null 結果が返されます。

```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |
