---
title: 文字列演算子-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの文字列演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.localizationpriority: high
ms.openlocfilehash: d7c975dcf3fb00ed1108f55957a35f494310203e
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513235"
---
# <a name="string-operators"></a>文字列演算子

Kusto には、文字列データ型を検索するためのさまざまなクエリ演算子が用意されています。 次の記事では、文字列用語のインデックスを作成する方法について説明し、文字列クエリ演算子の一覧を示し、パフォーマンスを最適化するためのヒントを示します。

## <a name="understanding-string-terms"></a>文字列用語について

Kusto は、型の列を含むすべての列にインデックスを付け `string` ます。 実際のデータに応じて、このような列に対して複数のインデックスが作成されます。 これらのインデックスは直接公開されませんが `string` `has` 、、、、などの名前の一部として使用される演算子を使用したクエリで使用され `has` `!has` `hasprefix` `!hasprefix` ます。 これらの演算子のセマンティクスは、列のエンコード方法によって決まります。 これらの演算子は、"プレーンな" 部分文字列の検索ではなく、 *用語* と一致します。

### <a name="what-is-a-term"></a>用語とは何ですか。 

既定では、各 `string` 値は ASCII 英数字の最大シーケンスに分割され、各シーケンスは用語になります。
たとえば、次の用語は、、、 `string` `Kusto` およびの `WilliamGates3rd` 各部分文字列で、、、、 `ad67d136` `c1db` `4f9f` `88ef` `d94f3b6b0b5a` です。

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto は、 *4 文字* 以上のすべての用語で構成される用語インデックスを構築します。このインデックスは、、などで使用され `has` `!has` ます。 クエリが4文字未満の語句を検索する場合、または演算子を使用する場合 `contains` 、Kusto は、一致するものが見つからない場合に、列の値のスキャンを元に戻します。 この方法は、用語インデックスで用語を検索するよりもはるかに低速です。

## <a name="operators-on-strings"></a>文字列に対する演算子

> [!NOTE]
> 次の表では、次の省略形が使用されています。
> * RHS = 式の右辺
> * LHS = 式の左辺
> 
> サフィックスが付いている演算子で `_cs` は大文字と小文字が区別されます。

演算子        |説明                                                       |大文字と小文字の区別|例 (`true` になる)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |等しい                                                            |はい           |`"aBc" == "aBc"`
`!=`            |等しくない                                                        |はい           |`"abc" != "ABC"`
`=~`            |等しい                                                            |いいえ            |`"abc" =~ "ABC"`
`!~`            |等しくない                                                        |いいえ            |`"aBc" !~ "xyz"`
`has`           |右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる     |いいえ            |`"North America" has "america"`
`!has`          |RHS が LHS の完全な用語ではない                                     |いいえ            |`"North America" !has "amer"` 
`has_cs`        |RHS は LHS の完全な用語です。                                        |はい           |`"North America" has_cs "America"`
`!has_cs`       |RHS が LHS の完全な用語ではない                                     |はい           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS は LHS の用語プレフィックスです                                       |いいえ            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS が LHS の用語プレフィックスではありません                                   |いいえ            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS は LHS の用語プレフィックスです                                       |はい           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS が LHS の用語プレフィックスではありません                                   |はい           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS は LHS の用語サフィックスです                                       |いいえ            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS は LHS の用語サフィックスではありません                                   |いいえ            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS は LHS の用語サフィックスです                                       |はい           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS は LHS の用語サフィックスではありません                                   |はい           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS は LHS のサブシーケンスとして出現します                                |いいえ            |`"FabriKam" contains "BRik"`
`!contains`     |RHS は LHS の中に出現しません                                         |いいえ            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS は LHS のサブシーケンスとして出現します                                |はい           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS は LHS の中に出現しません                                         |はい           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS は LHS の冒頭のサブシーケンスです                              |いいえ            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS は LHS の冒頭のサブシーケンスではありません                          |いいえ            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS は LHS の冒頭のサブシーケンスです                              |はい           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS は LHS の冒頭のサブシーケンスではありません                          |はい           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS は LHS の末尾のサブシーケンスです                               |いいえ            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS は LHS の末尾のサブシーケンスではありません                           |いいえ            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS は LHS の末尾のサブシーケンスです                               |はい           |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`  |RHS は LHS の末尾のサブシーケンスではありません                           |はい           |`"Fabrikam" !endswith_cs "brik"`
`matches regex` |LHS には RHS に対する一致が含まれています                                      |はい           |`"Fabrikam" matches regex "b.*k"`
`in`            |要素のいずれかに等しい                                     |はい           |`"abc" in ("123", "345", "abc")`
`!in`           |要素のいずれとも等しくない                                 |はい           |`"bca" !in ("123", "345", "abc")`
`in~`           |要素のいずれかに等しい                                     |いいえ            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |要素のいずれとも等しくない                                 |いいえ            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |と同じ `has` ですが、どの要素でも機能します。                    |いいえ            |`"North America" has_any("south", "north")`

> [!TIP]
> `has`文字列の一致ではなく、4つ以上の文字のインデックス付き *用語* に対して検索を含むすべての演算子。 文字列を ASCII 英数字のシーケンスに分割することによって、用語が作成されます。 「 [文字列用語につい](#understanding-string-terms)て」を参照してください。

## <a name="performance-tips"></a>パフォーマンスに関するヒント

パフォーマンスを向上させるために、同じタスクを実行する2つの演算子がある場合は、大文字と小文字を区別してを使用します。
例:

* で `=~` はなく、を使用します。 `==`
* で `in~` はなく、を使用します。 `in`
* で `contains` はなく、を使用します。 `contains_cs`

より高速な結果を得るために、英数字以外の文字でバインドされている記号や英数字の単語、またはフィールドの先頭または末尾があるかどうかをテストする場合は、またはを使用し `has` `in` ます。 
`has` は `contains` 、、、またはより高速に動作 `startswith` `endswith` します。

たとえば、次のクエリの最初の方が高速に実行されます。

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
