---
title: indexof_regex() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの indexof_regex() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6da0523e85bab4883c50708ffe3f7d087fdd8c8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513891"
---
# <a name="indexof_regex"></a>indexof_regex()

Function は、入力文字列内で指定された文字列が最初に出現した場合の 0 から始まるインデックスを報告します。 プレーン文字列の一致は重複しません。 

を[`indexof()`](indexoffunction.md)参照してください。

**構文**

`indexof_regex(`*ソース*`,`*ルックアップ*`[,`*length**start_index*`[,`start_index長さが*発生*しています`[,``]]])`

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
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5
|----|----|----|----|----
|0   |3   |-1  |-1  |    