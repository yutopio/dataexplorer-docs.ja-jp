---
title: arg_max() (集計関数) - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーの arg_max() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 73953a17b1819081c8458d5dbde3fa6d55c8866e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518957"
---
# <a name="arg_max-aggregation-function"></a>arg_max() (集計関数)

*ExprToMaximize*を最大化するグループ内の行を検索し *、ExprToReturn*の値を返`*`します (または行全体を返します)。

* 集計内の集計のコンテキストでのみ使用できます[。](summarizeoperator.md)

**構文**

`summarize`[`(`*名前を付ける*`,`*NameExprToReturn*`,``)=` `arg_max` ] `(`*エクスプルト最大化*, `*`  | *エクスプルトリターン*[`,` ..`)`

**引数**

* *ExprToMaximize*: 集計計算に使用される式。 
* *ExprToMaximize*が最大値*の場合に*値を返すために使用される式。 返す式は、入力テーブルのすべての列を返すワイルドカード (*) である場合があります。
* *名前 ExprprToMaximize*: *ExprToMaximize*を表す結果列の省略可能な名前。
* *名前を指定します*。 *ExprToReturn*

**戻り値**

*ExprToMaximize*を最大化するグループ内の行を検索し *、ExprToReturn*の値を返`*`します (または行全体を返します)。

**使用例**

[arg_min()](arg-min-aggfunction.md)集計関数の例を参照してください。