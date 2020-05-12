---
title: geo_geohash_to_central_point ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_geohash_to_central_point () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: eb59eae0bc014c6ce9060d65f6c3aced80e4275c
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227130"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

Geohash 四角形領域の中心を表す地理空間座標を計算します。

詳細については [`geohash`](https://en.wikipedia.org/wiki/Geohash) 、を参照してください。  

**構文**

`geo_geohash_to_central_point(`*geohash*`)`

**引数**

*geohash*: [geo_point_to_geohash ()](geo-point-to-geohash-function.md)によって計算されたときの geohash 文字列値。 Geohash 文字列には、1 ~ 18 文字を使用できます。

**戻り値**

[GeoJSON 形式](https://tools.ietf.org/html/rfc7946)および[動的](./scalar-data-types/dynamic.md)データ型の地理空間座標値。 Geohash が無効な場合は、クエリによって null の結果が生成されます。

> [!NOTE]
> GeoJSON 形式では、経度の最初と緯度の2番目のを指定します。

**使用例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|座標|経度 (longitude)|緯度 (latitude)|
|---|---|---|---|
|{<br>  "type": "Point",<br>  "座標": [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

次の例では、geohash 入力が無効であるため、null 結果が返されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print geohash = geo_geohash_to_central_point("a")
```

|geohash|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>例: Bing Maps の場所のディープリンクの作成

Geohash の値を使用して、geohash の中心点をポイントすることにより、Bing Maps へのディープリンク URL を作成することができます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// Use string concatenation to create Bing Map deep-link URL from a geo-point
let point_to_map_url = (_point:dynamic, _title:string) 
{
    strcat('https://www.bing.com/maps?sp=point.', _point.coordinates[1] ,'_', _point.coordinates[0], '_', url_encode(_title)) 
};
// Convert geohash to center point, and then use 'point_to_map_url' to create Bing Map deep-link
let geohash_to_map_url = (_geohash:string, _title:string)
{
    point_to_map_url(geo_geohash_to_central_point(_geohash), _title)
};
print geohash = 'sv8wzvy7'
| extend url = geohash_to_map_url(geohash, "You are here")
```

|geohash|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|
