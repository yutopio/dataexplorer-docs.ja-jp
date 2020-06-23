---
title: parse_json ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの parse_json () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac6bf9a8dbd54c3afca1c00f487e6ba564e65ce9
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264982"
---
# <a name="parse_json"></a>parse_json()

を `string` JSON 値として解釈し、値をとして返し `dynamic` ます。

JSON 複合オブジェクトの複数の要素を抽出する必要がある場合、この関数は[extractjson () 関数](./extractjsonfunction.md)よりも優れています。

**構文**

`parse_json(`*json*`)`

別名:
- [todynamic()](./todynamicfunction.md)
- [toobject ()](./todynamicfunction.md)

**引数**

* *json*: 型の式 `string` 。 これは、実際の値を表す、 [JSON 形式の値](https://json.org/)、または[dynamic](./scalar-data-types/dynamic.md)型の式を表し `dynamic` ます。

**戻り値**

`dynamic` *Json*の値によって決定される型のオブジェクト。
* *Json*の型がである場合 `dynamic` 、その値はその値として使用されます。
* *Json*の型が `string` で、が適切に[書式設定](https://json.org/)された json 文字列である場合、文字列が解析され、生成された値が返されます。
* *Json*の型が `string` であっても、[適切に書式設定](https://json.org/)された json 文字列ではない場合、戻り値は、元の値を保持する型のオブジェクトになり `dynamic` `string` ます。

**例**

次の例では、`context_custom_metrics` が `string` である場合、次のようになります。

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

次に、オブジェクト内のスロットの値を取得し、 `duration` 2 つのスロット `duration.value` `duration.min` ( `118.0` それぞれと) を取得し `110.0` ます。

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**ノート**

"スロット" の1つが別の JSON 文字列であるプロパティバッグを記述する JSON 文字列を使用するのが一般的です。 

次に例を示します。

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

このような場合は、を2回呼び出すだけで `parse_json` なく、2番目の呼び出しでが使用されるようにする必要が `tostring` あります。 それ以外の場合、への2回目の呼び出しは、宣言された `parse_json` 型がであるため、入力をそのまま出力に渡します `dynamic` 。

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
