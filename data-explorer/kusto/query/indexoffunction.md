---
title: indexof ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーにおける indexof () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8e237441d28f12ffc6f27f8a591980a701825e39
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347461"
---
# <a name="indexof"></a>indexof()

指定した文字列が入力文字列内で最初に見つかった位置の0から始まるインデックスをレポートします。

Lookup または入力文字列が*文字列*型でない場合、関数は強制的に値を*文字列*にキャストします。

詳細については、「[`indexof_regex()`](indexofregexfunction.md)」を参照してください。

## <a name="syntax"></a>構文

`indexof(`*ソース* `,`*参照* `[,`*start_index* `[,`*長さ* `[,`*発生回数*`]]])`

## <a name="arguments"></a>引数

* *source*: 入力文字列。  
* *lookup*: 検索する文字列。
* *start_index*: 開始位置を検索します。 省略可能。
* *length*: 検査する文字位置の数。 値-1 は無制限の長さを意味します。 省略可能。
* *オカレンス*: 発生回数。 既定値は1です。 省略可能。

## <a name="returns"></a>戻り値

*検索*の0から始まるインデックス位置。

入力に文字列が見つからない場合は、-1 を返します。

無関係 (0 未満) *start_index*、*出現*、または (-1 未満) の*長さ*のパラメーター-は*null*を返します。

## <a name="examples"></a>例
```kusto
print
 idx1 = indexof("abcdefg","cde")    // lookup found in input string
 , idx2 = indexof("abcdefg","cde",1,4) // lookup found in researched range 
 , idx3 = indexof("abcdefg","cde",1,2) // search starts from index 1, but stops after 2 chars, so full lookup can't be found
 , idx4 = indexof("abcdefg","cde",3,4) // search starts after occurrence of lookup
 , idx5 = indexof("abcdefg","cde",-1)  // invalid input
 , idx6 = indexof(1234567,5,1,4)       // two first parameters were forcibly casted to strings "12345" and "5"
 , idx7 = indexof("abcdefg","cde",2,-1)  // lookup found in input string
 , idx8 = indexof("abcdefgabcdefg", "cde", 1, 10, 2)   // lookup found in input range
 , idx9 = indexof("abcdefgabcdefg", "cde", 1, -1, 3)   // the third occurrence of lookup is not in researched range
```

|idx1|idx2|idx3|idx4|idx5|idx6|idx7|idx8|idx9|
|----|----|----|----|----|----|----|----|----|
|2   |2   |-1  |-1  |    |4   |2   |9   |-1  |
