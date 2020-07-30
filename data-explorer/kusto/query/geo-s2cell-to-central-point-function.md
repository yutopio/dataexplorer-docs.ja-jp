---
title: geo_s2cell_to_central_point ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_s2cell_to_central_point () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 30075b5a75e273061423a6f1540f44947ef93cec
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347716"
---
# <a name="geo_s2cell_to_central_point"></a>geo_s2cell_to_central_point()

S2 セルの中心を表す地理空間座標を計算します。

[S2 セル階層](https://s2geometry.io/devguide/s2cell_hierarchy)の詳細については、こちらをご覧ください。

## <a name="syntax"></a>構文

`geo_s2cell_to_central_point(`*s2cell*`)`

## <a name="arguments"></a>引数

*s2cell*: S2 セルトークン文字列値は[geo_point_to_s2cell ()](geo-point-to-s2cell-function.md)によって計算されています。 S2 セルトークンの最大文字列長は16文字です。

## <a name="returns"></a>戻り値

[GeoJSON 形式](https://tools.ietf.org/html/rfc7946)および[動的](./scalar-data-types/dynamic.md)データ型の地理空間座標値。 S2 セルトークンが無効な場合、クエリでは null 結果が生成されます。

> [!NOTE]
> GeoJSON 形式では、経度の最初と緯度の2番目のを指定します。

## <a name="examples"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("1234567")
| extend coordinates = point.coordinates
| extend longitude = coordinates[0], latitude = coordinates[1]
```

|point|座標|経度 (longitude)|緯度 (latitude)|
|---|---|---|---|
|{<br>  "type": "Point",<br>  "座標": [<br>    9.86830731850408,<br>    27.468392925827604<br>  ]<br>}|[<br>  9.86830731850408,<br>  27.468392925827604<br>]|9.86830731850408|27.4683929258276|

次の例では、無効な S2 セルトークン入力が原因で、null の結果が返されます。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print point = geo_s2cell_to_central_point("a")
```

|point|
|---|
||
