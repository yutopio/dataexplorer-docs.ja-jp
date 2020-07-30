---
title: strrep ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの strrep () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 797684aa70cb8205463cc41558afec82449c433d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350878"
---
# <a name="strrep"></a>strrep()

指定された[文字列](./scalar-data-types/string.md)を繰り返します。

* 最初または3番目の引数が文字列型でない場合は、強制的に文字列に変換されます。

## <a name="syntax"></a>構文

`strrep(`*値*、*乗数*、[*区切り記号*]`)`

## <a name="arguments"></a>引数

* *値*: 入力式
* *乗数*: 正の整数値 (1 ~ 1024)
* *delimiter*: 省略可能な文字列式 (既定値は空の文字列)

## <a name="returns"></a>戻り値

指定した回数だけ繰り返される値。*区切り記号*と連結されます。

*乗数*が許容される最大値 (1024) を超える場合、入力文字列は1024回繰り返されます。
 
## <a name="example"></a>例

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|