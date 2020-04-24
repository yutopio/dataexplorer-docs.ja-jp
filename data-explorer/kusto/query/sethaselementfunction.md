---
title: set_has_element() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでset_has_element() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 256e01646c6ecd39a8a589299acd6620008ece28
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507771"
---
# <a name="set_has_element"></a>set_has_element()

指定したセットに指定した要素が含まれているかどうかを判断します。

**構文**

`set_has_element(`*配列*,*値*`)`

**引数**

* *配列*: 検索する入力配列。
* *値*: 検索する値。 値は、 、 `long`、 `integer` `double`、 `datetime` `timespan`、 `decimal` `string`、 `guid`、 、 、 、または の値でなければなりません。

**戻り値**

値が配列内に存在するかどうかに応じて、True または false です。

**例**

```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|結果|
|---|
|1|

**参照**

配列内の値が存在する位置にも関心がある場合は[、array_index_of(arr,value)](arrayindexoffunction.md)を使用できます。 どちらの関数も、パフォーマンス面で同じです。