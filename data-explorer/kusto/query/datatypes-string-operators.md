---
title: 文字列演算子 - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの文字列演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91955ef5877e6c054917f70c655fc4b7cd3f277b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516475"
---
# <a name="string-operators"></a>文字列演算子

次の表は、文字列の演算子をまとめたものです。

演算子        |説明                                                       |大文字と小文字の区別|例 (`true` になる)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |等しい                                                            |はい           |`"aBc" == "aBc"`
`!=`            |等しくない                                                        |はい           |`"abc" != "ABC"`
`=~`            |等しい                                                            |いいえ            |`"abc" =~ "ABC"`
`!~`            |等しくない                                                        |いいえ            |`"aBc" !~ "xyz"`
`has`           |右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる     |いいえ            |`"North America" has "america"`
`!has`          |RHS が LHS に完全な単語として含まれない                                     |いいえ            |`"North America" !has "amer"` 
`has_cs`        |右辺 (RHS) が左辺 (LHS) に 1 つの単語として含まれる     |はい           |`"North America" has_cs "America"`
`!has_cs`       |RHS が LHS に完全な単語として含まれない                                     |はい           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS は LHS の用語接頭辞です                                       |いいえ            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS は LHS の用語接頭部ではありません                                   |いいえ            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS は LHS の用語接頭辞です                                       |はい           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS は LHS の用語接頭部ではありません                                   |はい           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS は LHS の用語サフィックスです                                       |いいえ            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS は LHS の用語サフィックスではありません                                   |いいえ            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS は LHS の用語サフィックスです                                       |はい           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS は LHS の用語サフィックスではありません                                   |はい           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS が LHS のサブシーケンスとして出現する                                |いいえ            |`"FabriKam" contains "BRik"`
`!contains`     |RHS が LHS 内に出現しない                                         |いいえ            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS が LHS のサブシーケンスとして出現する                                |はい           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS が LHS 内に出現しない                                         |はい           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS は LHS の冒頭のサブシーケンスです                              |いいえ            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS は LHS の初期サブシーケンスではありません。                          |いいえ            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS は LHS の冒頭のサブシーケンスです                              |はい           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS は LHS の初期サブシーケンスではありません。                          |はい           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS は LHS の末尾のサブシーケンスです                               |いいえ            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS は LHS の終了サブシーケンスではありません                           |いいえ            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS は LHS の末尾のサブシーケンスです                               |はい           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS は LHS の終了サブシーケンスではありません                           |はい           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS には RHS との一致が含まれている                                      |はい           |`"Fabrikam" matches regex "b.*k"`
`in`            |要素のいずれかに等しい                                     |はい           |`"abc" in ("123", "345", "abc")`
`!in`           |要素のいずれとも等しくない                                 |はい           |`"bca" !in ("123", "345", "abc")`
`in~`           |要素のいずれかに等しい                                     |いいえ            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |要素のいずれとも等しくない                                 |いいえ            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |同じ`has`が、任意の要素に動作します                    |いいえ            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>パフォーマンスに関するヒント

パフォーマンスを向上させるには、同じタスクを実行する 2 つの演算子がある場合は、大文字と小文字を区別する演算子を使用します。
次に例を示します。

* の`=~`代わりに、`==`
* の`in~`代わりに、`in`
* の`contains`代わりに、`contains_cs`

より高速な結果を得るには、英数字以外の文字 (またはフィールドの開始または終わり) で囲まれた記号または英数字の単語が存在することをテストする`has`場合`in`は、 または を使用します。 `has``contains`より高速に`startswith`実行 、`endswith`または より高速に実行されます。

たとえば、最初のクエリは高速に実行されます。

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>文字列用語について

既定では、 の列を含むすべての列のインデックスを作成します`string`。
実際、実際のデータに応じて、このような列に対して複数のインデックスが作成されます。 ユーザーには、これらのインデックスは直接公開されません (もちろん、クエリのパフォーマンスに対する肯定的な影響を除く) 名前の`string`一部として`has`持つ演算子を除いて`has`: `!has` `hasprefix`、 `!hasprefix`、 など。これらの演算子は、列のエンコード方法によってセマンティックが決まります。"プレーン" 部分文字列の一致を行う代わりに、これらの演算子は用語を一致**させます**。

用語ベースの一致を理解するには、まず用語が何であるかを理解する必要があります。 既定では、Kusto は`string`各値を最大の英数字シーケンスに分割し、各値は 1 つの用語にします。 たとえば`string`、次の用語は`Kusto`、 、`WilliamGates3rd`および次の部分文字列です。 `ad67d136` `c1db` `4f9f` `88ef` `d94f3b6b0b5a`

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

既定では、4 文字以上のすべての用語で構成される用語インデックスが作成され、このインデックスは、4 文字以上の`has`用語`!has`を検索するときに 、 などによって使用されます。 (クエリが 4 文字未満の用語を検索する場合、または`contains`演算子を使用する場合、一致を判別できない場合は、列の値をスキャンするように戻ります。

