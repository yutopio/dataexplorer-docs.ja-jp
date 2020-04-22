---
title: geo_point_to_geohash() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの geo_point_to_geohash() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: b69d22c56cef4b54a99aa9aa3e9897a2ef177834
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030117"
---
# <a name="geo_point_to_geohash"></a>geo_point_to_geohash()

地理的位置のジオハッシュ文字列値を計算します。

Geohash の詳細については、[ここ](https://en.wikipedia.org/wiki/Geohash)を参照してください。  

**構文**

`geo_point_to_geohash(`*経度*`, `*緯度*`, `[*精度*]`)`

**引数**

* *経度*: 地理的位置の経度の値。 x が実数で、範囲が [-180, +180] の場合、経度 x は有効と見なされます。 
* *緯度*: 地理的位置の緯度値。 y が実数で y が範囲 [-90, +90] の場合、緯度 y は有効と見なされます。 
* *正確さ*:`int`要求された精度を定義するオプションです。 サポートされている値は [1,18] の範囲です。 指定されていない場合は、既定値`5`が使用されます。

**戻り値**

要求された精度の長さを持つ、指定された地理的位置の Geohash 文字列値。 座標または精度が無効な場合、クエリは空の結果を生成します。

> [!NOTE]
>
> * 地理ハッシュは、地理空間クラスタリングツールとして便利です。
> * Geohashは、最も低いレベル18で最高レベル1から0.6 μ²の2500万km²の範囲の面積カバレッジで18の精度レベルを有します。
> * Geohashes の共通接頭辞は、互いにポイントが近接していることを示します。 共有プレフィックスが長いほど、2 つの場所が近くなります。 精度の値は、ジオハッシュの長さに変換されます。
> * ジオハッシュは平面サーフェス上の矩形領域です。
> * 経度 x と緯度 y で計算された geohash 文字列に対して[geo_geohash_to_central_point()](geo-geohash-to-central-point-function.md)関数を呼び出しても、必ずしも x と y が返されるとは限りません。
> * Geohash 定義により、2 つの地理的位置が互いに非常に近いが、異なる Geohash コードを持つ可能性があります。

**精度値ごとのジオハッシュ矩形領域カバレッジ:**

| 精度 | 幅     | [高さ]    |
|----------|-----------|-----------|
| 1        | 5000 km   | 5000 km   |
| 2        | 1250 km   | 625 km    |
| 3        | 156.25 km | 156.25 km |
| 4        | 39.06 km  | 19.53 km  |
| 5        | 4.88 km   | 4.88 km   |
| 6        | 1.22 km   | 0.61 km   |
| 7        | 152.59メートル  | 152.59メートル  |
| 8        | 38.15メートル   | 19.07メートル   |
| 9        | 4.77メートル    | 4.77メートル    |
| 10       | 1.19メートル    | 0.59メートル    |
| 11       | 149.01 mm | 149.01 mm |
| 12       | 37.25 mm  | 18.63ミリメートル  |
| 13       | 4.66 mm   | 4.66 mm   |
| 14       | 1.16 mm   | 0.58 mm   |
| 15       | 145.52 μ  | 145.52 μ  |
| 16       | 36.28 μ   | 18.19 μ   |
| 17       | 4.55 μ    | 4.55 μ    |
| 18       | 1.14 μ    | 0.57 μ    |

[geo_point_to_s2cell()](geo-point-to-s2cell-function.md)も参照してください。

**使用例**

geohash によって集計された米国の嵐イベント。

:::image type="content" source="images/geo-point-to-geohash-function/geohash.png" alt-text="米国のジオハッシュ":::

```kusto
StormEvents
| project BeginLon, BeginLat
| summarize by hash=geo_point_to_geohash(BeginLon, BeginLat, 3)
| project geo_geohash_to_central_point(hash)
| render scatterchart with (kind=map) // map rendering available in Kusto Explorer desktop
```

```kusto
print geohash = geo_point_to_geohash(139.806115, 35.554128, 12)  
```

| ジオハッシュ      |
|--------------|
| xn76m27ty9g4 |

```kusto
print geohash = geo_point_to_geohash(-80.195829, 25.802215, 8)
```

|ジオハッシュ|
|---|
|dhwfz15h|

次の例では、座標のグループを検索します。 グループ内のすべての座標のペアは、4.88 km の四角形の領域に存在します。
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

| ジオハッシュ | count | locations  |
|---------|-------|------------|
| c23n8   | 2     | ["A","B"] |
| c23n9   | 1     | ["C"]      |

次の例では、無効な座標入力が原因で空の結果が生成されます。
```kusto
print geohash = geo_point_to_geohash(200,1,8)
```

| ジオハッシュ |
|---------|
|         |

次の例では、不正確な入力が無効なため、空の結果が生成されます。
```kusto
print geohash = geo_point_to_geohash(1,1,int(null))
```

| ジオハッシュ |
|---------|
|         |