---
title: 文字列演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの文字列演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e16d90c8307392536b5971758ce7df15a9ea1b46
ms.sourcegitcommit: ef009294b386cba909aa56d7bd2275a3e971322f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977135"
---
# <a name="string-operators"></a>文字列演算子

次の表は、文字列に対する演算子をまとめたものです。

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
`hasprefix`     |RHS は LHS の用語プレフィックスです                                       |いいえ            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS は LHS の用語プレフィックスではありません                                   |いいえ            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS は LHS の用語プレフィックスです                                       |はい           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS は LHS の用語プレフィックスではありません                                   |はい           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS は LHS の用語サフィックスです                                       |いいえ            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS は LHS の用語サフィックスではありません                                   |いいえ            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS は LHS の用語サフィックスです                                       |はい           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS は LHS の用語サフィックスではありません                                   |はい           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS が LHS のサブシーケンスとして出現する                                |いいえ            |`"FabriKam" contains "BRik"`
`!contains`     |RHS が LHS 内に出現しない                                         |いいえ            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS が LHS のサブシーケンスとして出現する                                |はい           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS が LHS 内に出現しない                                         |はい           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS は LHS の冒頭のサブシーケンスです                              |いいえ            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS は LHS の最初のサブシーケンスではありません                          |いいえ            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS は LHS の冒頭のサブシーケンスです                              |はい           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS は LHS の最初のサブシーケンスではありません                          |はい           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS は LHS の末尾のサブシーケンスです                               |いいえ            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS は LHS の終わりのサブシーケンスではありません                           |いいえ            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS は LHS の末尾のサブシーケンスです                               |はい           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS は LHS の終わりのサブシーケンスではありません                           |はい           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS には RHS との一致が含まれている                                      |はい           |`"Fabrikam" matches regex "b.*k"`
`in`            |要素のいずれかに等しい                                     |はい           |`"abc" in ("123", "345", "abc")`
`!in`           |要素のいずれとも等しくない                                 |はい           |`"bca" !in ("123", "345", "abc")`
`in~`           |要素のいずれかに等しい                                     |いいえ            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |要素のいずれとも等しくない                                 |いいえ            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |と同じ`has`ですが、どの要素でも機能します。                    |いいえ            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>パフォーマンスに関するヒント

パフォーマンスを向上させるために、同じタスクを実行する2つの演算子がある場合は、大文字と小文字を区別してを使用します。
次に例を示します。

* で`=~`はなく、を使用します。`==`
* で`in~`はなく、を使用します。`in`
* で`contains`はなく、を使用します。`contains_cs`

より高速な結果を得るために、英数字以外の文字 (またはフィールドの先頭または末尾) によってバインドされている記号や英数字の単語が`has`ある`in`かどうかをテストする場合は、またはを使用します。 `has`は、、 `contains`、 `startswith`または`endswith`より高速に実行します。

たとえば、最初のクエリはより高速に実行されます。

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>文字列用語について

既定では、Kusto は、型`string`の列を含むすべての列にインデックスを設定します。
実際のデータによっては、このような列に対して複数のインデックスが作成されます。 ユーザーにとって、 `string`これらのインデックスは`has` `has`、名前の一部として使用される演算子 (、 `!has`、 `hasprefix`、 `!hasprefix`など) を除き、直接公開されることはありません (もちろん、クエリのパフォーマンスに与える影響はありません)。これらの演算子は、その意味が列のエンコード方法によって決まるという点で特別です。これらの演算子は、"プレーンな" 部分文字列の検索ではなく、**用語**と一致します。

用語ベースの一致を理解するには、最初に用語とは何かを理解する必要があります。 既定では、Kusto は`string` 、各値を ASCII 英数字の最大シーケンスに分割し、それぞれの値を1つの用語に変換します。 たとえば、次`string`の用語`Kusto` `WilliamGates3rd`は、、、およびの各部分文字列`ad67d136` `c1db` `4f9f` `88ef`で、、、、 `d94f3b6b0b5a`です。

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

既定では、kusto は、 **4**文字以上のすべての用語で構成される用語インデックスを構築します。 `has`この`!has`インデックスは、、などによって使用されます。これは、4文字以上の用語も検索する場合に使用します。 (クエリが4文字未満の語句を検索する場合、または演算子を`contains`使用する場合、Kusto は一致を判別できない場合に、列の値のスキャンを元に戻します。これは、用語インデックスでの用語の検索よりもはるかに低速です)。

