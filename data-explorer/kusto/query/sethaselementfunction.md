---
title: set_has_element ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの set_has_element () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 8e96b97754600d308526a4cb059907521fda0521
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862930"
---
# <a name="set_has_element"></a>set_has_element()

指定したセットに指定した要素が含まれているかどうかを判断します。

**構文**

`set_has_element(`*array*、*value*`)`

**引数**

* *array*: 検索する入力配列。
* *値*: 検索対象の値。 値は、、、、、、、 `long` `integer` `double` `datetime` `timespan` `decimal` `string` 、または `guid` 型である必要があります。

**戻り値**

値が配列内に存在するかどうかによって、True または false になります。

**例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|結果|
|---|
|1|

**参照**

[`array_index_of(arr, value)`](arrayindexoffunction.md)配列内の値が存在する位置を検索するには、を使用します。 どちらの関数も、同等のパフォーマンスを備えています。
