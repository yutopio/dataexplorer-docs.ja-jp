---
title: 文字列演算子 - Azure Data Explorer
description: この記事では、Azure Data Explorer の文字列演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 13dac735127815c00ac8c1128c710e26208406d7
ms.sourcegitcommit: d4b359e817e002fba7320132732ce6d9cee97415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541497"
---
# <a name="string-operators"></a>文字列演算子

Kusto には、文字列データ型を検索するためのさまざまなクエリ演算子が用意されています。 以下の記事では、文字列用語にインデックスを付ける方法、文字列クエリ演算子の一覧、パフォーマンスを最適化するためのヒントを示します。

## <a name="understanding-string-terms"></a>文字列用語について

Kusto では、`string` 型の列を含むすべての列にインデックスが付けられます。 実際のデータに応じて、このような列には複数のインデックスが作成されます。 これらのインデックスは直接公開されませんが、`has`、`!has`、`hasprefix`、`!hasprefix` など、名前に `has` が含まれる `string` 演算子によるクエリで使用されます。 これらの演算子のセマンティクスは、列のエンコード方法によって決まります。 これらの演算子を使用すると、"プレーンな" 部分文字列の一致ではなく、"*用語*" の一致が行われます。

### <a name="what-is-a-term"></a>用語とは 

既定では、各 `string` 値は ASCII 英数字の最大シーケンスに分割され、各シーケンスが用語になります。
たとえば、次の `string` の場合、用語は `Kusto` と `WilliamGates3rd` で、`ad67d136`、`c1db`、`4f9f`、`88ef`、`d94f3b6b0b5a` は部分文字列です。

```
Kusto: ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto の場合、"*4 文字以上*" のすべての用語で構成される用語インデックスが構築され、このインデックスは `has`、`!has` などで使用されます。 クエリで 4 文字未満の用語が検索される場合、または `contains` 演算子が使用される場合、Kusto は、一致するものが見つからない場合は、列の値のスキャンに戻ります。 この方法は、用語インデックスでの用語の検索よりはるかに低速です。

## <a name="operators-on-strings"></a>文字列の演算子

> [!NOTE]
> 以下の表では次の省略形が使用されています。
> * RHS = 式の右辺
> * LHS = 式の左辺
> 
> `_cs` というサフィックスが付いている演算子では、大文字と小文字が区別されます。

> [!NOTE]
> 大文字と小文字を区別しない演算子は、現在 ASCII テキストでのみサポートされています。 ASCII 以外の比較には、[tolower()](tolowerfunction.md) 関数を使用してください。

演算子        |説明                                                       |大文字と小文字の区別|例 (`true` になる)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |等しい                                                            |はい           |`"aBc" == "aBc"`
`!=`            |等しくない                                                        |はい           |`"abc" != "ABC"`
`=~`            |等しい                                                            |いいえ            |`"abc" =~ "ABC"`
`!~`            |等しくない                                                        |いいえ            |`"aBc" !~ "xyz"`
`has`           |右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる     |いいえ            |`"North America" has "america"`
`!has`          |RHS が LHS に完全な用語として含まれない                                     |いいえ            |`"North America" !has "amer"` 
[`has_any`](has-anyoperator.md)       |`has` と同じだが、任意の要素で機能する                    |いいえ            |`"North America" has_any("south", "north")`
`has_cs`        |RHS は LHS 内の完全な用語である                                        |はい           |`"North America" has_cs "America"`
`!has_cs`       |RHS が LHS に完全な用語として含まれない                                     |はい           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS は LHS に用語のプレフィックスとして含まれる                                       |いいえ            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS は LHS に用語のプレフィックスとして含まれない                                   |いいえ            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS は LHS に用語のプレフィックスとして含まれる                                       |はい           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS は LHS に用語のプレフィックスとして含まれない                                   |はい           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS は LHS に用語のサフィックスとして含まれる                                       |いいえ            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS は LHS に用語のサフィックスとして含まれない                                   |いいえ            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS は LHS に用語のサフィックスとして含まれる                                       |はい           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS は LHS に用語のサフィックスとして含まれない                                   |はい           |`"North America" !hassuffix_cs "icA"`
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
[`in`](inoperator.md)            |要素のいずれかに等しい                                     |はい           |`"abc" in ("123", "345", "abc")`
[`!in`](inoperator.md)           |要素のいずれとも等しくない                                 |はい           |`"bca" !in ("123", "345", "abc")`
`in~`           |要素のいずれかに等しい                                     |いいえ            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |要素のいずれとも等しくない                                 |いいえ            |`"bca" !in~ ("123", "345", "ABC")`


> [!TIP]
> `has` を含むすべての演算子の場合は、インデックスが付けられた 4 文字以上の "*用語*" が検索され、部分文字列の一致は検索されません。 用語は、文字列を ASCII 英数字のシーケンスに分割することによって作成されます。 「[文字列用語について](#understanding-string-terms)」を参照してください。

## <a name="performance-tips"></a>パフォーマンスに関するヒント

パフォーマンスを向上させるには、同じタスクを実行する 2 つの演算子がある場合は、大文字と小文字が区別されるものを使用します。
例:

* `=~` の代わりに `==` を使用します
* `in~` の代わりに `in` を使用します
* `contains` の代わりに `contains_cs` を使用します

より速く結果を得るには、非英数字またはフィールドの開始か終了によって囲まれた記号または英数字の単語の存在をテストする場合は、`has` または `in` を使用します。 
`has` は、`contains`、`startswith`、または `endswith` より速く動作します。

たとえば、次のクエリは 1 番目の方が速く実行されます。

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
