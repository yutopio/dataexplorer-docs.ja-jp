---
title: geo_s2cell_to_central_point() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの geo_s2cell_to_central_point() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 624d163b508768d0a5316ee3ec62a12b11942176
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514435"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

S2 セルの中心を表す地理空間座標を計算します。

S2 セルの詳細については、[ここを](http://s2geometry.io/devguide/s2cell_hierarchy)クリックしてください。

**構文**

`geo_s2cell_to_central_point(`*s2セル*`)`

**引数**

*s2cell*: s2 セル トークン文字列値は[geo_point_to_s2cell()](geo-point-to-s2cell-function.md)で計算されたとおりです。 S2 Cell トークンの最大文字列長は 16 文字です。

**戻り値**

[GeoJSON 形式](https://tools.ietf.org/html/rfc7946)および[動的](./scalar-data-types/dynamic.md)データ型の地理空間座標値。 S2 セル トークンが無効な場合、クエリは null の結果を生成します。

> [!NOTE]
> GeoJSON 形式では、経度が最初に、緯度が 2 番目に指定されます。

**使用例**

```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|座標|経度 (longitude)|緯度 (latitude)|
|---|---|---|---|
|{<br>  "タイプ": "ポイント"、<br>  "座標" : [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

次の例では、S2 セル トークンの入力が無効なため、null の結果を返します。
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||