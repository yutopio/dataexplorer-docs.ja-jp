---
title: indexof_regex ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの indexof_regex () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 85c39128eeb9b6ded38366ccd3bea228820c67a7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347444"
---
# <a name="indexof_regex"></a>indexof_regex()

関数は、入力文字列内で指定した文字列が最初に出現する位置の0から始まるインデックスを報告します。 文字列の一致は重複しません。

[`indexof()`](indexoffunction.md) を参照してください。

## <a name="syntax"></a>構文

`indexof_regex(`*ソース* `,`*参照* `[,`*start_index* `[,`*長さ* `[,`*発生回数*`]]])`

## <a name="arguments"></a>引数

|引数     | 説明                                     |必須またはオプション|
|--------------|-------------------------------------------------|--------------------|
|source        | 入力文字列                                    |必須            |
|参照        | シークする文字列                                  |必須            |
|start_index   | 検索開始位置                           |省略可能            |
|length        | 調べる文字位置の数。 -1 は無制限の長さを定義します |省略可能            |
|occurrence    | パターンの N 番目の外観のインデックスを検索します。 
                 既定値は1です。最初に見つかった位置のインデックスです。 |省略可能            |

## <a name="returns"></a>戻り値

*検索*の0から始まるインデックス位置。

* 入力に文字列が見つからない場合は、-1 を返します。
* 次の場合は*null*を返します。
     * start_index が0未満です。
     * 出現回数が0未満です。
     * 長さのパラメーターが-1 未満です。


## <a name="examples"></a>例

```kusto
print
 idx1 = indexof_regex("abcabc", "a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", "a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", "a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", "a.a", 0, -1, 2)  // Plain string matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", "a|ab", -1)  // invalid input
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
