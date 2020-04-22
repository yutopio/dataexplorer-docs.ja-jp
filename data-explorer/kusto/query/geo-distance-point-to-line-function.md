---
title: geo_distance_point_to_line() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで geo_distance_point_to_line() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: 34c6811ed5a51dae5281a4374421df5914c5b263
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663784"
---
# <a name="geo_distance_point_to_line"></a>geo_distance_point_to_line()

座標と地球上の線の最短距離を計算します。

**構文**

`geo_distance_point_to_line(`*経度*`, `*緯度*`, `*線文字列*`)`

**引数**

* *経度*: 地理空間座標の経度の値 (度単位)。 有効な値は実数で、範囲 [-180, +180] です。
* *緯度*: 地理空間座標の緯度値 (度)。 有効な値は実数で、範囲 [-90, +90] です。
* *lineString*: [GeoJSON 形式](https://tools.ietf.org/html/rfc7946)の行と[動的](./scalar-data-types/dynamic.md)データ型の行。

**戻り値**

地球上の座標と線の間の最短距離(メートル単位)。 座標または lineString が無効な場合、クエリは null の結果を生成します。

> [!NOTE]
> * 地理空間座標は[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標参照系によって表される座標として解釈されます。
> * 地球上の距離を測定するために使用される[測地データム](https://en.wikipedia.org/wiki/Geodetic_datum)は球体です。 ライン エッジは球体上の測地線です。

**ラインストリングの定義と制約**

dynamic({"type": "LineString",座標": [lng_1,lat_1]、lng_2,lat_2],...,[lng_N,lat_N]]}

* LineString 座標配列には、少なくとも 2 つのエントリが含まれている必要があります。
* 座標 [経度、緯度] は、経度が範囲 [-180, +180] の実数で、緯度が範囲 [-90, +90] の実数である場合、有効である必要があります。
* エッジ長は 180 度未満にする必要があります。 2 つの頂点間の最短エッジが選択されます。

> [!TIP]
> パフォーマンスを向上させるには、リテラル行を使用します。

**使用例**

次の例では、ノース ラスベガス空港と近くの道路の間の最短距離を検索します。

:::image type="content" source="images/queries/geo/distance_point_to_line.png" alt-text="ノースラスベガス空港と道路間の距離":::

```kusto
print distance_in_meters = geo_distance_point_to_line(-115.199625, 36.210419, dynamic({ "type":"LineString","coordinates":[[-115.115385,36.229195],[-115.136995,36.200366],[-115.140252,36.192470],[-115.143558,36.188523],[-115.144076,36.181954],[-115.154662,36.174483],[-115.166431,36.176388],[-115.183289,36.175007],[-115.192612,36.176736],[-115.202485,36.173439],[-115.225355,36.174365]]}))
```

| distance_in_meters |
|--------------------|
| 3797.88887253334   |

南海岸米国の嵐のイベント。 イベントは、定義された海岸線から 5 km の最大距離でフィルタリングされます。

:::image type="content" source="images/queries/geo/us_south_coast_storm_events.png" alt-text="米国南海岸の嵐のイベント":::

```kusto
let southCoast = dynamic({"type":"LineString","coordinates":[[-97.18505859374999,25.997549919572112],[-97.58056640625,26.96124577052697],[-97.119140625,27.955591004642553],[-94.04296874999999,29.726222319395504],[-92.98828125,29.82158272057499],[-89.18701171875,29.11377539511439],[-89.384765625,30.315987718557867],[-87.5830078125,30.221101852485987],[-86.484375,30.4297295750316],[-85.1220703125,29.6880527498568],[-84.00146484374999,30.14512718337613],[-82.6611328125,28.806173508854776],[-82.81494140625,28.033197847676377],[-82.177734375,26.52956523826758],[-80.9912109375,25.20494115356912]]});
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_distance_point_to_line(BeginLon, BeginLat, southCoast) < 5000
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

NYタクシーのピックアップ。 ピックアップは、定義されたラインから最大 0.1 m の距離でフィルタリングされます。

:::image type="content" source="images/queries/geo/park_ave_ny_road.png" alt-text="米国南海岸の嵐のイベント":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_distance_point_to_line(pickup_longitude, pickup_latitude, dynamic({"type":"LineString","coordinates":[[-73.97583961486816,40.75486445336327],[-73.95215034484863,40.78743027428638]]})) <= 0.1
| take 50
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

次の例では、無効な LineString 入力のため、null の結果を返します。
```kusto
print distance_in_meters = geo_distance_point_to_line(1,1, dynamic({ "type":"LineString"}))
```

| distance_in_meters |
|--------------------|
|                    |

次の例では、無効な座標入力のため、null の結果を返します。
```kusto
print distance_in_meters = geo_distance_point_to_line(300, 3, dynamic({ "type":"LineString","coordinates":[[1,1],[2,2]]}))
```

| distance_in_meters |
|--------------------|
|                    |