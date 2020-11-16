---
title: indexof_regex ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの indexof_regex () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5826920a411235166002b6833ed88869fb85f211
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638973"
---
# <a name="indexof_regex"></a>indexof_regex()

入力文字列内で最初に見つかった指定の検索正規表現の0から始まるインデックスを返します。

[`indexof()`](indexoffunction.md)に関するページを参照してください。

## <a name="syntax"></a>構文

`indexof_regex(`*ソース* `,`*参照* `[,`*start_index* `[,`*長さ* `[,`*発生回数*`]]])`

## <a name="arguments"></a>引数

|引数     | 説明                                     |必須またはオプション|
|--------------|-------------------------------------------------|--------------------|
|ソース        | 入力文字列                                    |必須            |
|参照        | 正規表現の参照文字列。               |必須            |
|start_index   | 検索開始位置                           |オプション            |
|length        | 調べる文字位置の数。 -1 は無制限の長さを定義します |オプション            |
|occurrence    | パターンの N 番目の外観のインデックスを検索します。 
                 既定値は1です。最初に見つかった位置のインデックスです。 |オプション            |

## <a name="returns"></a>戻り値

*検索* の0から始まるインデックス位置。

* 入力に文字列が見つからない場合は、-1 を返します。
* 次の場合は *null* を返します。
     * start_index が0未満です。
     * 出現回数が0未満です。
     * 長さのパラメーターが-1 未満です。

> [!NOTE]
- 重複する検索の参照はサポートされていません。
- 正規表現文字列には、エスケープするか、または @ ' ' 文字列リテラルを使用する必要がある文字を含めることができます。

## <a name="examples"></a>例

```kusto
print
 idx1 = indexof_regex("abcabc", @"a.c") // lookup found in input string
 , idx2 = indexof_regex("abcabcdefg", @"a.c", 0, 9, 2)  // lookup found in input string
 , idx3 = indexof_regex("abcabc", @"a.c", 1, -1, 2)  // there is no second occurrence in the search range
 , idx4 = indexof_regex("ababaa", @"a.a", 0, -1, 2)  // Matches do not overlap so full lookup can't be found
 , idx5 = indexof_regex("abcabc", @"a|ab", -1)  // invalid start_index argument
```

|idx1|idx2|idx3|idx4|idx5|
|----|----|----|----|----|
|0   |3   |-1  |-1  |    |
