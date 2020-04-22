---
title: parse_xml() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの parse_xml() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9743cbcb8d3c25707d97a4a6844f947352e63b0b
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744618"
---
# <a name="parse_xml"></a>parse_xml()

を`string`XML 値として解釈し、値を JSON に変換して値を`dynamic`返します。

**構文**

`parse_xml(`*xml*`)`

**引数**

* *xml*: XML`string`形式の値を表す型 の式。

**戻り値**

XML 形式が無効な場合は*xml*または null の値によって決定される[dynamic](./scalar-data-types/dynamic.md)型のオブジェクト。

XML から JSON への変換は[、xml2json](https://github.com/Cheedoong/xml2json)ライブラリを使用して行います。

変換は次のように行われます。

XML                                |JSON                                            |アクセス
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | { "e" : null }                                  | o.e
`<e>text</e>`                      | { "e" : "テキスト" }                                | o.e
`<e name="value" />`               | { "e":{"@name": "値"} }                     | o.e["@name"]
`<e name="value">text</e>`         | { "e" :@name{ " " " " "" "値" ", "#text" : "テキスト" } } | o.e["@name"" o.e["#text" ] ]
`<e> <a>text</a> <b>text</b> </e>` | { "e": { "a" : "テキスト"、"b": "テキスト" } }          | o.e.a o.e.b
`<e> <a>text</a> <a>text</a> </e>` | { "e": { "a": ["テキスト","テキスト"} } }             | o.e.a[0] o.e.a[1]
`<e> text <a>text</a> </e>`        | { "e" : { "#text" : "テキスト"、"a": "text" } }      | 1'o.e[#text"] o.e.a

**メモ**

* 最大入力`string`長`parse_xml`は 128 KB です。 長い文字列の解釈は、null オブジェクトになります。 
* 要素ノード、属性、およびテキストノードのみが変換されます。 他のすべてがスキップされます
 
**例**

次の例では、`context_custom_metrics` が `string` である場合、次のようになります。 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<duration>
    <value>118.0</value>
    <count>5.0</count>
    <min>100.0</min>
    <max>150.0</max>
    <stdDev>0.0</stdDev>
    <sampledValue>118.0</sampledValue>
    <sum>118.0</sum>
</duration>
```

次の CSL フラグメントは、XML を次の JSON に変換します。

```json
{
    "duration": {
        "value": 118.0,
        "count": 5.0,
        "min": 100.0,
        "max": 150.0,
        "stdDev": 0.0,
        "sampledValue": 118.0,
        "sum": 118.0
    }
}
```

オブジェクト内の`duration`スロットの値を取得し、そこから 2 つのスロット`duration.value`と`duration.min`(`118.0`と`110.0`) を取得します。

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```