---
title: parse_json() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでparse_json() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87c2155864f039fb5b261786d0fa6eb6770af2f
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744680"
---
# <a name="parse_json"></a>parse_json()

を`string`JSON 値として解釈し、値を`dynamic`として返します。 

JSON 複合オブジェクトの複数の要素を抽出する必要がある場合は[、extractjson() 関数](./extractjsonfunction.md)を使用するよりも優れています。

**構文**

`parse_json(`*Json*`)`

別名:
- [todynamic()](./todynamicfunction.md)
- [トオブジェクト()](./todynamicfunction.md)

**引数**

* *json*: JSON`string`[形式の値](https://json.org/)を表す型 、または実際`dynamic`の値を表す[dynamic](./scalar-data-types/dynamic.md)型の式。

**戻り値**

json の値`dynamic`によって決定される型のオブジェクト。 *json*
* *json*が型`dynamic`の場合、その値は、その値をそのとおりに使用します。
* *json*が type`string`で[、適切な形式の JSON 文字列](https://json.org/)である場合、文字列は解析され、生成された値が返されます。
* *json*が`string`型であるが、適切な[形式の JSON 文字列](https://json.org/)**でない**場合、戻り値は元`string`の値を保持`dynamic`する型のオブジェクトです。

**例**

次の例では、`context_custom_metrics` が `string` である場合、次のようになります。 

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

次の CSL フラグメントは、オブジェクト内の`duration`スロットの値を取得し、そこから 2 つの`duration.value`スロットと`duration.min`(`118.0`と`110.0`) をそれぞれ取得します。

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**メモ**

「スロット」の 1 つが別の JSON 文字列であるプロパティ バッグを記述する JSON 文字列を持つことは、やや一般的です。 次に例を示します。

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

このような場合は、2 回呼び出す`parse_json`だけでなく、2 番目の呼び出し`tostring`で使用されることを確認する必要があります。 それ以外の場合、2`parse_json`番目の呼び出しは、宣言された型が`dynamic`:

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```