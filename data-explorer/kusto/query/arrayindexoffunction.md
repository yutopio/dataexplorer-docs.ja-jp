---
title: array_index_of ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの array_index_of () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: a366120428d14c713b18aee6652460817c75433a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349569"
---
# <a name="array_index_of"></a>array_index_of()

指定した項目を配列内で検索し、その位置を返します。

## <a name="syntax"></a>構文

`array_index_of(`*array*、*value*`)`

## <a name="arguments"></a>引数

* *array*: 検索する入力配列。
* *値*: 検索対象の値。 値の型は long、integer、double、datetime、timespan、decimal、string、または guid である必要があります。

## <a name="returns"></a>戻り値

検索の0から始まるインデックス位置。
値が配列に見つからない場合は、-1 を返します。

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_index_of(arr, "example")
```

|結果|
|---|
|3|

**参照**

配列に値が存在するかどうかを確認するだけで、その位置に関心がない場合は、 [set_has_element ( `arr` 、 `value` )](sethaselementfunction.md)を使用できます。 この関数を使用すると、クエリの読みやすさが向上します。 両方の関数のパフォーマンスは同じです。
