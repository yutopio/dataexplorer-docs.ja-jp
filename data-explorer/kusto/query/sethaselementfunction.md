---
title: set_has_element ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの set_has_element () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: b32fd7361d98c47c8e93814db6b794a762fad1cf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247205"
---
# <a name="set_has_element"></a>set_has_element()

指定したセットに指定した要素が含まれているかどうかを判断します。

## <a name="syntax"></a>構文

`set_has_element(`*array*、*value*`)`

## <a name="arguments"></a>引数

* *array*: 検索する入力配列。
* *値*: 検索対象の値。 値は、、、、、、、 `long` `integer` `double` `datetime` `timespan` `decimal` `string` 、または `guid` 型である必要があります。

## <a name="returns"></a>戻り値

値が配列内に存在するかどうかによって、True または false になります。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=set_has_element(arr, "example")
```

|結果|
|---|
|1|

## <a name="see-also"></a>関連項目

[`array_index_of(arr, value)`](arrayindexoffunction.md)配列内の値が存在する位置を検索するには、を使用します。 どちらの関数も、同等のパフォーマンスを備えています。
