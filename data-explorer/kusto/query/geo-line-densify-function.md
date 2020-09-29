---
title: geo_line_densify ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_line_densify () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: f86ec0349b4e84215e9b2fdff33b2d705967bcac
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452818"
---
# <a name="geo_line_densify"></a>geo_line_densify()

中間点を追加して、平面線または複数行のエッジを geodesics に変換します。

## <a name="syntax"></a>構文

`geo_line_densify(`*lineString* `, `*許容範囲*`)`

## <a name="arguments"></a>引数

* *lineString*: [GeoJSON 形式](https://tools.ietf.org/html/rfc7946) および [動的](./scalar-data-types/dynamic.md) データ型の行または複数行。
* *tolerance*: 元の平面エッジと変換された測地エッジチェーンの間の最大距離をメートル単位で定義する、省略可能な数値です。 サポートされている値の範囲は [0.1, 1万] です。 指定しない場合は、既定値 `10` が使用されます。

## <a name="returns"></a>戻り値

[GeoJSON 形式](https://tools.ietf.org/html/rfc7946)の Densified 行と[動的](./scalar-data-types/dynamic.md)データ型。 行または許容範囲が無効である場合は、クエリによって null の結果が生成されます。

> [!NOTE]
> * 地理空間座標は、 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home) 座標参照システムによって表されるものとして解釈されます。

**LineString の定義**

動的 ({"type": "LineString", "座標": [[lng_1, lat_1], [lng_2, lat_2],..., [lng_N, lat_N]]})

動的 ({"type": "MultiLineString", "コーディネート": [[line_1, line_2,..., line_N]]})

* LineString 座標配列には、少なくとも2つのエントリが含まれている必要があります。
* 座標 [経度、緯度] は有効である必要があります。 経度は [-180, + 180] の範囲の実数である必要があり、緯度は [-90, + 90] の範囲の実数である必要があります。
* エッジの長さは180度未満でなければなりません。 2つの頂点の間の最短のエッジが選択されます。

**Constraints**

* Densified 行のポイントの最大数は、10485760に制限されています。
* [動的](./scalar-data-types/dynamic.md)な形式の行の格納にはサイズの制限があります。

**目的**

* [GeoJSON 形式](https://tools.ietf.org/html/rfc7946) では、2つの点の間のエッジを、直線デカルト直線として定義します。
* 測地または平面エッジを使用するかどうかの決定は、データセットによって異なり、特に長いエッジに関連しています。

## <a name="examples"></a>例

次の例では、マンハッタンアイランド内の道路を densifies しています。 エッジは短く、平面エッジとその測地の間の距離が、tolerance によって指定された距離を下回っています。 そのため、結果は変更されません。

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[-73.949247, 40.796860],[-73.973017, 40.764323]]})))
```

|densified_line|
|---|
|{"type": "LineString", "コーディネート": [[-73.949247, 40.796860], [-73.973017, 40.764323]]}|

次の例では、~ 130km の長さのエッジを densifies します。

```kusto
print densified_line = tostring(geo_line_densify(dynamic({"type":"LineString","coordinates":[[50, 50], [51, 51]]})))
```

|densified_line|
|---|
|{"type": "LineString"、"座標": [[50, 50]、[50.125, 50.125]、[50.25, 50.25]、[50.375, 50.375]、[50.5, 50.5]、[50.625, 50.625]、[50.75, 50.75]、[50.875, 50.875]、[51, 51]]}|

次の例では、座標入力が無効であるため、null 結果が返されます。

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[300,1],[1,1]]}))
```

|densified_line|
|---|
||

次の例では、許容範囲の入力が無効であるため、null 結果が返されます。

```kusto
print densified_line = geo_line_densify(dynamic({"type":"LineString","coordinates":[[1,1],[2,2]]}), 0)
```

|densified_line|
|---|
||
