---
title: 文字列演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの文字列演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6718ad614166d5328dcd412d09405c1db8cc14dc
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265084"
---
# <a name="string-operators"></a>文字列演算子

次の表は、文字列に対する演算子をまとめたものです。

演算子        |説明                                                       |大文字と小文字の区別|例 (`true` になる)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |次の値に等しい                                                            |はい           |`"aBc" == "aBc"`
`!=`            |等しくない                                                        |Yes           |`"abc" != "ABC"`
`=~`            |等しい                                                            |いいえ            |`"abc" =~ "ABC"`
`!~`            |等しくない                                                        |いいえ            |`"aBc" !~ "xyz"`
`has`           |右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる     |No            |`"North America" has "america"`
`!has`          |RHS が LHS の完全な用語ではない                                     |No            |`"North America" !has "amer"` 
`has_cs`        |右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる     |Yes           |`"North America" has_cs "America"`
`!has_cs`       |RHS が LHS の完全な用語ではない                                     |Yes           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS は LHS の用語プレフィックスです                                       |No            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS が LHS の用語プレフィックスではありません                                   |No            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS は LHS の用語プレフィックスです                                       |Yes           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS が LHS の用語プレフィックスではありません                                   |Yes           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS は LHS の用語サフィックスです                                       |No            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS は LHS の用語サフィックスではありません                                   |No            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS は LHS の用語サフィックスです                                       |Yes           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS は LHS の用語サフィックスではありません                                   |Yes           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS が LHS のサブシーケンスとして出現する                                |No            |`"FabriKam" contains "BRik"`
`!contains`     |RHS は LHS の中に出現しません                                         |No            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS が LHS のサブシーケンスとして出現する                                |Yes           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS は LHS の中に出現しません                                         |Yes           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS は LHS の冒頭のサブシーケンスです                              |No            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS は LHS の冒頭のサブシーケンスではありません                          |No            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS は LHS の冒頭のサブシーケンスです                              |Yes           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS は LHS の冒頭のサブシーケンスではありません                          |Yes           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS は LHS の末尾のサブシーケンスです                               |No            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS は LHS の末尾のサブシーケンスではありません                           |No            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS は LHS の末尾のサブシーケンスです                               |Yes           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS は LHS の末尾のサブシーケンスではありません                           |Yes           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS には RHS との一致が含まれている                                      |Yes           |`"Fabrikam" matches regex "b.*k"`
`in`            |要素のいずれかに等しい                                     |はい           |`"abc" in ("123", "345", "abc")`
`!in`           |要素のいずれとも等しくない                                 |はい           |`"bca" !in ("123", "345", "abc")`
`in~`           |要素のいずれかに等しい                                     |No            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |要素のいずれとも等しくない                                 |No            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |と同じ `has` ですが、どの要素でも機能します。                    |No            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>パフォーマンスに関するヒント

パフォーマンスを向上させるために、同じタスクを実行する2つの演算子がある場合は、大文字と小文字を区別してを使用します。
次に例を示します。

* で `=~` はなく、を使用します。`==`
* で `in~` はなく、を使用します。`in`
* で `contains` はなく、を使用します。`contains_cs`

より高速な結果を得るために、英数字以外の文字でバインドされている記号や英数字の単語、またはフィールドの先頭または末尾があるかどうかをテストする場合は、またはを使用し `has` `in` ます。 
`has`は `contains` 、、、またはより高速に動作 `startswith` `endswith` します。

たとえば、次のクエリの最初の方が高速に実行されます。

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>文字列用語について

既定では、Kusto は、型の列を含むすべての列にインデックスを設定 `string` します。
実際のデータに応じて、このような列に対して複数のインデックスが作成されます。 これらのインデックスは、(、、、などの `string` `has` 名前の一部として使用される演算子を除く) 直接公開されません `has` `!has` `hasprefix` `!hasprefix` 。
これらの演算子は特別な意味を持ちます。セマンティクスは、列のエンコード方法によって決まります。 これらの演算子は、"プレーンな" 部分文字列の検索ではなく、**用語**と一致します。

用語ベースの一致を理解するには、まず用語の意味を理解しておく必要があります。 既定では、各 `string` 値は ASCII 英数字の最大シーケンスに分割され、各シーケンスは用語になります。

たとえば、次の用語は、、、 `string` `Kusto` およびの `WilliamGates3rd` 各部分文字列で、、、、 `ad67d136` `c1db` `4f9f` `88ef` `d94f3b6b0b5a` です。

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

既定では、Kusto は、 **4**文字以上のすべての用語で構成される用語インデックスを構築します。このインデックスは `has` 、4文字以上の語句を検索するときに、、などで使用され `!has` ます。
クエリが4文字未満の語句を検索する場合、または演算子を使用する場合 `contains` 、Kusto は、一致するものが見つからない場合に、列の値のスキャンを元に戻します。 この方法は、用語インデックスで用語を検索するよりもはるかに低速です。
