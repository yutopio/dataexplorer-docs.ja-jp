---
title: geo_point_in_circle() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでgeo_point_in_circle() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: ca3ca8a1ac2299c43888ac827d46c25d02e4999d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663793"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

空間座標が地球上の円の内側にあるかどうかを計算します。

**構文**

`geo_point_in_circle(`*p_longitude*`, ``, `*pc_latitude*`, `*c_radius* pc_latitudec_radiusp_latitude`, `*pc_longitudep_latitudep_longitudep_latitude**pc_longitude*`)`

**引数**

* *p_longitude*: 地理空間座標の経度の値 (度単位)。 有効な値は実数で、範囲 [-180, +180] です。
* *p_latitude*: 地理空間座標の緯度値 (度単位)。 有効な値は実数で、範囲 [-90, +90] です。
* *pc_longitude*: 円中心の地理空間座標経度の値 (度単位)。 有効な値は実数で、範囲 [-180, +180] です。
* *pc_latitude*: 円中心の地理空間座標の緯度値 (度単位)。 有効な値は実数で、範囲 [-90, +90] です。
* *c_radius*: メートル単位の円半径。 有効な値は正の値でなければなりません。

**戻り値**

地理空間座標が円の内側にあるかどうかを示します。 座標または円が無効な場合、クエリは null の結果を生成します。

> [!NOTE]
>* 地理空間座標は[WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標参照系によって表される座標として解釈されます。
>* 地球上の距離を測定するために使用される[測地データム](https://en.wikipedia.org/wiki/Geodetic_datum)は球体です。
>* 円は地球上の球状のキャップです。 キャップの半径は、球のサーフェスに沿って計測されます。

**使用例**

次のクエリは、中心が [-122.317404、 47.609119] 座標の半径 18 km の円で定義された領域内のすべての場所を検索します。

:::image type="content" source="images/queries/geo/circle_seattle.png" alt-text="シアトル付近の場所":::

```kusto
datatable(longitude:real, latitude:real, place:string)
[
    real(-122.317404), 47.609119, 'Seattle',                   // In circle 
    real(-123.497688), 47.458098, 'Olympic National Forest',   // In exterior of circle  
    real(-122.201741), 47.677084, 'Kirkland',                  // In circle
    real(-122.443663), 47.247092, 'Tacoma',                    // In exterior of circle
    real(-122.121975), 47.671345, 'Redmond',                   // In circle
]
| where geo_point_in_circle(longitude, latitude, -122.317404, 47.609119, 18000)
| project place
```

|place|
|---|
|Seattle|
|CoreNet|
|レドモンド|

オーランドの嵐のイベント。 イベントはオーランド座標で 100km 以内でフィルタリングされ、イベントタイプとハッシュで集計されます。

:::image type="content" source="images/queries/geo/orlando_storm_events.png" alt-text="オーランドの嵐のイベント":::

```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

次の例は、近隣の 10 m 以内にある NY タクシーのピックアップを示しています。 関連するピックアップはハッシュで集計されます。

:::image type="content" source="images/queries/geo/circle_junction.png" alt-text="NYタクシー近くのピックアップ":::

```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

次の例では true を返します。
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

次の例では false を返します。
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

次の例では、無効な座標入力のため、null の結果を返します。
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

次の例では、円半径入力が無効なため、null の結果が返されます。
```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||