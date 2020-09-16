---
title: parse_xml ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの parse_xml () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 41e3f58ba857e23d31062484f11f30e80fb37317
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680736"
---
# <a name="parse_xml"></a>parse_xml()

を `string` XML 値として解釈し、値を JSON に変換して、値をとして返し `dynamic` ます。

## <a name="syntax"></a>構文

`parse_xml(`*xml*`)`

## <a name="arguments"></a>引数

* *xml*: `string` xml 形式の値を表す型の式。

## <a name="returns"></a>戻り値

*Xml*の値によって決定される[動的](./scalar-data-types/dynamic.md)型のオブジェクト。 xml 形式が無効な場合は null。

変換は次のように行われます。

XML                                |JSON                                            |アクセス
-----------------------------------|------------------------------------------------|--------------         
`<e/>`                             | {"e": null}                                  | o. e
`<e>text</e>`                      | {"e": "text"}                                | o. e
`<e name="value" />`               | {"e": {" @name ": "value"}}                     | o. e [" @name "]
`<e name="value">text</e>`         | {"e": {" @name ": "value", "#text": "text"}} | o. e [" @name "] o. e ["#text"]
`<e> <a>text</a> <b>text</b> </e>` | {"e": {"a": "text", "b": "text"}}          | o.. e. a. b
`<e> <a>text</a> <a>text</a> </e>` | {"e": {"a": ["text", "text"]}}             | o. a [0] o. a [1]
`<e> text <a>text</a> </e>`        | {"e": {"#text": "text", "a": "text"}}      | 1 ' o. e ["#text"] o. e.

**ノート**

* の最大入力 `string` 長 `parse_xml` は 1 mb (1048576 バイト) です。 長い文字列を解釈すると、null オブジェクトになります。
* 要素ノード、属性、およびテキストノードのみが変換されます。 他のすべての操作はスキップされます
 
## <a name="example"></a>例

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

その後、次の CSL フラグメントは XML を次の JSON に変換します。

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

とは、オブジェクト内のスロットの値を取得し、 `duration` 2 つのスロット (それぞれと) を取得し `duration.value` `duration.min` `118.0` `110.0` ます。

```kusto
T
| extend d=parse_xml(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```
