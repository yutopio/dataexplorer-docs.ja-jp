---
title: geo_polygon_densify ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの geo_polygon_densify () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mbrichko
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: bdb7bda617085ae1a7b3ead60c46c80c883943f4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347784"
---
# <a name="geo_polygon_densify"></a>geo_polygon_densify()

中間点を追加することによって、多角形または multipolygon 平面のエッジを geodesics に変換します。

## <a name="syntax"></a>構文

`geo_polygon_densify(`*polygon* `, `*許容範囲*`)`

## <a name="arguments"></a>引数

* *polygon*: [GeoJSON 形式](https://tools.ietf.org/html/rfc7946)および[動的](./scalar-data-types/dynamic.md)データ型の多角形または multipolygon。
* *tolerance*: 元の平面エッジと変換された測地エッジチェーンの間の最大距離をメートル単位で定義する、省略可能な数値です。 サポートされている値の範囲は [0.1, 1万] です。 指定しない場合は、既定値 `10` が使用されます。

## <a name="returns"></a>戻り値

[GeoJSON 形式](https://tools.ietf.org/html/rfc7946)の Densified polygon と[動的](./scalar-data-types/dynamic.md)データ型。 Polygon または tolerance が無効な場合は、クエリによって null の結果が生成されます。

> [!NOTE]
> * 地理空間座標は、 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php?action=home)座標参照システムによって表されるものとして解釈されます。
> * 多角形は正しく定義されている必要がありますが、関数は多角形の有効性をチェックしません。

**Polygon の定義**

動的 ({"type": "Polygon", "座標": [LinearRingShell, LinearRingHole_1,..., LinearRingHole_N]})

動的 ({"type": "MultiPolygon", "座標": [[LinearRingShell, LinearRingHole_1,..., LinearRingHole_N],..., [LinearRingShell, LinearRingHole_1,..., LinearRingHole_M]]})

* LinearRingShell は必須であり、順序付けされた座標配列として定義されています [ `counterclockwise` [lng_1、lat_1],..., [lng_i、lat_i],..., [lng_j、lat_j],..., [lng_1、lat_1]]。 シェルは1つしか存在できません。
* LinearRingHole は省略可能であり、座標の順序付き配列として定義されてい `clockwise` ます [[lng_1、lat_1],..., [lng_i、lat_i],..., [lng_j、lat_j],..., [lng_1、lat_1]]。 任意の数の内部リングと穴を使用できます。
* LinearRing 頂点は、3つ以上の座標と区別する必要があります。 最初の座標は、最後の座標と同じである必要があります。 少なくとも4つのエントリが必要です。
* 座標 [経度、緯度] は有効である必要があります。 経度は [-180, + 180] の範囲の実数である必要があり、緯度は [-90, + 90] の範囲の実数である必要があります。
* LinearRingShell は球の半分に囲まれています。 LinearRing は球を2つのリージョンに分割します。 2つのリージョンのうち、小さい方が選択されます。
* LinearRing エッジの長さは180度未満でなければなりません。 2つの頂点の間の最短のエッジが選択されます。

**制約**

* Densified polygon 内の点の最大数は、10485760に制限されています。
* [動的](./scalar-data-types/dynamic.md)な形式でのポリゴンの格納にはサイズの制限があります。
* 有効な多角形を指定すると無効になる可能性があります。 アルゴリズムによって、点が一様でない方法で追加されるため、エッジが相互に切り替わる可能性があります。

**目的**

* [GeoJSON 形式](https://tools.ietf.org/html/rfc7946)では、2つの点の間のエッジを、直線デカルト直線として定義します。
* 測地または平面エッジを使用するかどうかの決定は、データセットによって異なり、特に長いエッジに関連しています。

## <a name="examples"></a>例

次の例では、densifies マンハッタン Central 公園 polygon です。 エッジは短く、平面エッジと、それに対応する測地の距離は、許容範囲で指定された距離よりも小さくなります。 そのため、結果は変更されません。

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[-73.958244,40.800719],[-73.949146,40.79695],[-73.973093,40.764226],[-73.982062,40.768159],[-73.958244,40.800719]]]})))
```

|densified_polygon|
|---|
|{"type": "Polygon", "座標": [[-73.958244, 40.800719], [-73.949146, 40.79695], [-73.973093, 40.764226], [-73.982062, 40.768159], [-73.958244, 40.800719]]]}|

次の例では、多角形の2つのエッジを densifies します。 Densified エッジの長さは約 110 km です

```kusto
print densified_polygon = tostring(geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]})))
```

|densified_polygon|
|---|
|{"type": "Polygon", "コーディネート": [[10, 10], [10.25, 10], [10.5, 10], [10.75, 10], [11, 10], [11, 11], [10.75, 11], [10.5, 11], [10.25, 11], [10, 11], [10, 10]]]}|

次の例では、座標入力が無効であるため、null 結果が返されます。

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,900],[11,10],[11,11],[10,11],[10,10]]]}))
```

|densified_polygon|
|---|
||

次の例では、許容範囲の入力が無効であるため、null 結果が返されます。

```kusto
print densified_polygon = geo_polygon_densify(dynamic({"type":"Polygon","coordinates":[[[10,10],[11,10],[11,11],[10,11],[10,10]]]}), 0)
```

|densified_polygon|
|---|
||
