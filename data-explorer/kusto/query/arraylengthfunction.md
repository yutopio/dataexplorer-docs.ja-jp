---
title: array_length ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの array_length () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3777194270e91d75d68c3af544e18b62358f709e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246780"
---
# <a name="array_length"></a>array_length()

動的配列内の要素の数を計算します。

## <a name="syntax"></a>構文

`array_length(`*array*`)`

## <a name="arguments"></a>引数

* *array*: `dynamic` 値。

## <a name="returns"></a>戻り値

*array* 内の要素数。*array* が配列ではない場合は `null`。

## <a name="examples"></a>使用例

```kusto
print array_length(parse_json('[1, 2, 3, "four"]')) == 4

print array_length(parse_json('[8]')) == 1

print array_length(parse_json('[{}]')) == 1

print array_length(parse_json('[]')) == 0

print array_length(parse_json('{}')) == null

print array_length(parse_json('21')) == null
```