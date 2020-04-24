---
title: geo_geohash_to_central_point() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでgeo_geohash_to_central_point() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: a71265b58cffcec2fcf9d6ac8d412c8dd44866f4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514639"
---
# <a name="geo_geohash_to_central_point"></a>geo_geohash_to_central_point()

ジオハッシュ四角形領域の中心を表す地理空間座標を計算します。

Geohash の詳細については、[ウィキペディア](https://en.wikipedia.org/wiki/Geohash)を参照してください。  

**構文**

`geo_geohash_to_central_point(`*ジオハッシュ*`)`

**引数**

*geohash*: [geo_point_to_geohash()](geo-point-to-geohash-function.md)で計算されたジオハッシュ文字列値。 ジオハッシュ文字列は 1 ~ 18 文字です。

**戻り値**

[GeoJSON 形式](https://tools.ietf.org/html/rfc7946)および[動的](./scalar-data-types/dynamic.md)データ型の地理空間座標値。 ジオハッシュが無効な場合、クエリは null の結果を生成します。

> [!NOTE]
> GeoJSON 形式では、経度が最初に、緯度が 2 番目に指定されます。

**使用例**

```kusto
print point = geo_geohash_to_central_point("sunny")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|座標|経度 (longitude)|緯度 (latitude)|
|---|---|---|---|
|{<br>  "タイプ": "ポイント"、<br>  "座標" : [<br>    42.47314453125,<br>    23.70849609375<br>  ]<br>}|[<br>  42.47314453125,<br>  23.70849609375<br>]|42.47314453125|23.70849609375|

次の例では、無効な geohash 入力が原因で null の結果を返します。

```kusto
print geohash = geo_geohash_to_central_point("a")
```

|ジオハッシュ|
|---|
||

## <a name="example-creating-location-deep-links-for-bing-maps"></a>例: Bing マップのディープ リンクの場所の作成

[ジオハッシュ] 値を使用して、ジオハッシュの中心点をポイントしてマップをBingするディープリンク URL を作成できます。

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

|ジオハッシュ|url|
|---|---|
|sv8wzvy7|[https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here](https://www.bing.com/maps?sp=point.32.15620994567871_34.80245590209961_You+are+here)|