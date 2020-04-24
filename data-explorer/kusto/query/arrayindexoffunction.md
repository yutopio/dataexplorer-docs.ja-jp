---
title: array_index_of() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでarray_index_of() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f68c9385c55cedb4491033d137af087dafd26aa0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518617"
---
# <a name="array_index_of"></a>array_index_of()

指定された項目を配列で検索し、その位置を返します。

**構文**

`array_index_of(`*配列*,*値*`)`

**引数**

* *配列*: 検索する入力配列。
* *値*: 検索する値。 値は、長整数、倍精度浮動小数点型、日付/時刻、タイムスパン、10 進数、文字列、または GUID の型にする必要があります。

**戻り値**

検索のインデックス位置が 0 から始まります。
値が配列内に見つからない場合は -1 を返します。

**例**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|結果|
|---|
|3|

**参照**

配列内に値が存在するかどうかを確認するだけで、その位置に関心がない場合は[、set_has_element(arr, value)を](sethaselementfunction.md)使用できます。