---
title: array_split() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで array_split() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/28/2018
ms.openlocfilehash: 4d5d8ce5e918f335f1f26f4e3fc045ab0fa6e3a1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518566"
---
# <a name="array_split"></a>array_split()

分割インデックスに従って配列を複数の配列に分割し、生成された配列を動的配列にパックします。

**構文**

`array_split(`*arr*,*インデックス*`)`

**引数**

* *arr*: 分割する入力配列は動的配列でなければなりません。
* *インデックス*: 整数または整数の動的配列 (ゼロから始まる) を基に、負の値はarray_length + 値に変換されます。

**戻り値**

範囲 [0.i1] の値を持つ N+1 配列を含む動的配列 (i1..i2)、..[iN..array_length) arr から、ここで N は入力インデックス数と i1.iN はインデックスです。

**使用例**

1.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, 2)
```
|Arr|arr_split|
|---|---|
|[1,2,3,4,5]|[[1,2],[3,4,5]]|



2.
```kusto
print arr=dynamic([1,2,3,4,5]) 
| extend arr_split=array_split(arr, dynamic([1,3]))
```
|Arr|arr_split|
|---|---|
|[1,2,3,4,5]|[[1],[2,3],[4,5]]|