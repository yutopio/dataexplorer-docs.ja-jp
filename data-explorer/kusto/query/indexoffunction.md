---
title: インデックスの() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの indexof() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 698cc7c13c3d665f9f5cfe25a31269dc763c51fb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513942"
---
# <a name="indexof"></a>indexof()

Function は、入力文字列内で指定された文字列が最初に出現した場合の 0 から始まるインデックスを報告します。

検索文字列または入力文字列が文字列型でない場合は、強制的に値を文字列にキャストします。

を[`indexof_regex()`](indexofregexfunction.md)参照してください。

**構文**

`indexof(`*ソース*`,`*ルックアップ*`[,`*length**start_index*`[,`start_index長さが*発生*しています`[,``]]])`

**引数**

* *ソース*: 入力文字列。  
* *検索*: シークする文字列。
* *start_index*: 検索開始位置 (オプション)。
* *長さ*: 検査する文字位置の数、-1 は無制限の長さを定義します (オプション)。
* *オカレンス*: は、発生するデフォルト 1 (オプション) です。

**戻り値**

*検索*のインデックス位置を 0 から始めます。

入力に文字列が見つからない場合は -1 を返します。

無関係 (0 未満) *start_index*の*場合、または*(-1 未満)*長さ*パラメーター - *null*を返します。

**使用例**
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
