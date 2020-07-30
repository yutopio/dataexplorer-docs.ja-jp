---
title: geo_point_to_geohash ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_point_to_geohash () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 7998726637a7d19413954a509dd0ad9b34202f03
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347767"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

地理的な場所の geohash 文字列値を計算します。

[Geohash](https://en.wikipedia.org/wiki/Geohash)の詳細については、こちらをご覧ください。  

## <a name="syntax"></a>構文

`geo_point_to_geohash(`*経度* `, `*緯度* `, `[*精度*]`)`

## <a name="arguments"></a>引数

* *経度*: 地理的な場所の経度の値。 "X" が実数で、[-180, + 180] の範囲内にある場合、経度 x は有効と見なされます。 
* *緯度*: 地理的な場所の緯度の値。 Y が実数で、y が範囲 [-90, + 90] の場合、緯度 y は有効と見なされます。 
* *精度*: 要求された精度を定義する省略可能な `int` 。 サポートされている値は [1, 18] の範囲内です。 指定しない場合は、既定値 `5` が使用されます。

## <a name="returns"></a>戻り値

要求された精度の長さを持つ、指定された地理的な場所の geohash 文字列値。 座標または精度が無効な場合は、クエリによって空の結果が生成されます。

> [!NOTE]
>
> * Geohash は、便利な地理空間クラスタリングツールにすることができます。
> * Geohash には、2500万 km²から最下位レベル18の0.6 μ²までの範囲内の領域カバレッジを持つ18の精度レベルがあります。
> * Geohash の共通プレフィックスは、互いのポイントの距離を示します。 共有プレフィックスが長いほど、2つの場所が近くになります。 精度の値は、geohash の長さに変換されます。
> * Geohash は、平面サーフェイス上の四角形の領域です。
> * 経度 x と緯度 y で計算された geohash 文字列で[geo_geohash_to_central_point ()](geo-geohash-to-central-point-function.md)関数を呼び出すと、必ずしも x と y が返されるとは限りません。
> * Geohash の定義により、2つの地理的な場所が互いに近接していても、異なる geohash コードを持っている可能性があります。

**精度の値ごとの geohash 四角形領域カバレッジ:**

| 精度 | 幅     | [高さ]    |
|----------|-----------|-----------|
| 1        | 5000 km   | 5000 km   |
| 2        | 1250 km   | 625 km    |
| 3        | 156.25 km | 156.25 km |
| 4        | 39.06 km  | 19.53 km  |
| 5        | 4.88 km   | 4.88 km   |
| 6        | 1.22 km   | 0.61 km   |
| 7        | 152.59 m  | 152.59 m  |
| 8        | 38.15 m   | 19.07 m   |
| 9        | 4.77 m    | 4.77 m    |
| 10       | 1.19 m    | 0.59 m    |
| 11       | 149.01 mm | 149.01 mm |
| 12       | 37.25 mm  | 18.63 mm  |
| 13       | 4.66 mm   | 4.66 mm   |
| 14       | 1.16 mm   | 0.58 mm   |
| 15       | 145.52 μ  | 145.52 μ  |
| 16       | 36.28 μ   | 18.19 μ   |
| 17       | 4.55 μ    | 4.55 μ    |
| 18       | 1.14 μ    | 0.57 μ    |

「 [Geo_point_to_s2cell ()](geo-point-to-s2cell-function.md)」も参照してください。

## <a name="examples"></a>例

Geohash によって集計された米国の嵐イベント。

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="米国 geohash":::

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| geohash      |
|--------------|
| xn76m27ty9g4 |

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|geohash|
|---|
|dhwfz15h|

次の例では、座標のグループを検索します。 グループ内の座標の各ペアは、4.88 km (4.88 km) の四角形の領域に存在します。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(location_id:string, longitude:real, latitude:real)
[
  "A", double(-122.303404), 47.570482,
  "B", double(-122.304745), 47.567052,
  "C", double(-122.278156), 47.566936,
]
| summarize count = count(),                                          // items per group count
            locations = make_list(location_id)                        // items in the group
            by geohash = geo_point_to_geohash(longitude, latitude)    // geohash of the group
```

| geohash | count | locations  |
|---------|-------|------------|
| c23n8   | 2     | ["A", "B"] |
| c23n9   | 1     | ["C"]      |

次の例では、座標入力が無効なため、空の結果が生成されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| geohash |
|---------|
|         |

次の例では、精度入力が無効なため、空の結果が生成されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| geohash |
|---------|
|         |
