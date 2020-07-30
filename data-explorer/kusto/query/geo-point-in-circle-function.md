---
title: geo_point_in_circle ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_point_in_circle () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: 1e94e8eca72e6cb679a84e7b91ea376b9ec4b29c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347818"
---
# <a name="geo_point_in_circle"></a>geo_point_in_circle()

空間座標が地球上の円の内側にあるかどうかを計算します。

## <a name="syntax"></a>構文

`geo_point_in_circle(`*p_longitude* `, `*p_latitude* `, `*pc_longitude* `, `*pc_latitude* `, `*c_radius*`)`

## <a name="arguments"></a>引数

* *p_longitude*: 地理空間座標の経度の値 (度)。 有効な値は、実数と範囲 [-180, + 180] です。
* *p_latitude*: 地理空間座標緯度の値 (度)。 有効な値は、実数と範囲 [-90, + 90] です。
* *pc_longitude*: 円の中心地理空間座標経度の値 (度単位)。 有効な値は、実数と範囲 [-180, + 180] です。
* *pc_latitude*: 円中心の地理空間座標の緯度の値 (度)。 有効な値は、実数と範囲 [-90, + 90] です。
* *c_radius*: 円の半径 (メートル単位)。 有効な値は正の値である必要があります。

## <a name="returns"></a>戻り値

地理空間座標が円の内側にあるかどうかを示します。 座標または円が無効な場合は、クエリによって null の結果が生成されます。

> [!NOTE]
>* 地理空間座標は、 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標参照システムによって表されるものとして解釈されます。
>* 地球上の距離を測定するために使用される[測地 datum](https://en.wikipedia.org/wiki/Geodetic_datum)は球です。
>* 円は地球の球キャップです。 キャップの半径は球の表面に沿って測定されます。

## <a name="examples"></a>例

次のクエリでは、領域内のすべての場所が検索されます。半径は 18 km、center は [-122.317404, 47.609119] 座標です。

:::image type="content" source="images/geo-point-in-circle-function/circle-seattle.png" alt-text="シアトル近くに配置":::

<!-- csl: https://help.kusto.windows.net/Samples -->
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

州オーランドの嵐イベント。 イベントは、州オーランド座標内の 100 km によってフィルター処理され、イベントの種類とハッシュによって集計されます。

:::image type="content" source="images/geo-point-in-circle-function/orlando-storm-events.png" alt-text="州オーランドの嵐イベント":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat, EventType
| where geo_point_in_circle(BeginLon, BeginLat, real(-81.3891), 28.5346, 1000 * 100)
| summarize count() by EventType, hash = geo_point_to_s2cell(BeginLon, BeginLat)
| project geo_s2cell_to_central_point(hash), EventType, count_
| render piechart with (kind=map) // map rendering available in Kusto Explorer desktop
```

次の例では、特定の場所の10メートル以内での NY タクシーを示しています。 関連するピックアップは、ハッシュによって集計されます。

:::image type="content" source="images/geo-point-in-circle-function/circle-junction.png" alt-text="NY の近くにある NY タクシー":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
nyc_taxi
| project pickup_longitude, pickup_latitude
| where geo_point_in_circle( pickup_longitude, pickup_latitude, real(-73.9928), 40.7429, 10)
| summarize by hash = geo_point_to_s2cell(pickup_longitude, pickup_latitude, 22)
| project geo_s2cell_to_central_point(hash)
| render scatterchart with (kind = map) // map rendering available in Kusto Explorer desktop
```

次の例では、true が返されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.143564, 47.535677, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|1|

次の例では、false が返されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(-122.137575, 47.630683, -122.100896, 47.527351, 3500)
```

|in_circle|
|---|
|0|

次の例では、座標入力が無効なため、null 結果が返されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print in_circle = geo_point_in_circle(200, 1, 1, 1, 1)
```

|in_circle|
|---|
||

次の例では、円の半径入力が無効なため、null 結果が返されます。

```kusto
print in_circle = geo_point_in_circle(1, 1, 1, 1, -1)
```

|in_circle|
|---|
||
