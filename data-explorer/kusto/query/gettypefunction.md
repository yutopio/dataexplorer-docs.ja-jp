---
title: gettype ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの gettype () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 84aa0d815328532f7a627958b56c41b998665d6b
ms.sourcegitcommit: f7bebd245081a5cdc08e88fa4f9a769c18e13e5d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94644605"
---
# <a name="gettype"></a>gettype()

1つの引数のランタイム型を返します。

ランタイム型は、名目的な型がである式の場合、標準 (静的) 型とは異なる場合があります。 `dynamic` このような場合は、 `gettype()` 実際の値の型 (値がメモリにエンコードされているかどうか) を明らかにするのに役立ちます。

## <a name="syntax"></a>構文

`gettype(`*With*`)`

## <a name="returns"></a>戻り値

1つの引数のランタイム型を表す文字列。

## <a name="examples"></a>例

|Expression                          |戻り値      |
|------------------------------------|-------------|
|`gettype("a")`                      |`string`     |
|`gettype(111)`                      |`long`       |
|`gettype(1==1)`                     |`bool`       |
|`gettype(now())`                    |`datetime`   |
|`gettype(1s)`                       |`timespan`   |
|`gettype(parse_json('1'))`           |`int`        |
|`gettype(parse_json(' "abc" '))`     |`string`     |
|`gettype(parse_json(' {"abc":1} '))` |`dictionary` | 
|`gettype(parse_json(' [1, 2, 3] '))` |`array`      |
|`gettype(123.45)`                   |`real`       |
|`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))`|`guid`| 
|`gettype(parse_json(''))`            |`null`|
