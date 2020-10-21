---
title: arg_max () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの arg_max () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: bd3d0b2c9d0c68ae646960b0b4b0b3ca41c0bb43
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248144"
---
# <a name="arg_max-aggregation-function"></a>arg_max () (集計関数)

*Exprtomaximize*を最大化し、 *ExprToReturn*の値を返す (または行全体を返す) グループ内の行を検索し `*` ます。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize`[ `(` *Nameexprtomaximize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_max` `(`*Exprtomaximize*、 `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>引数

* *Exprtomaximize*: 集計計算に使用される式。 
* *ExprToReturn*: *exprtomaximize* が最大の場合に値を返すために使用される式。 返される式は、入力テーブルのすべての列を返すためのワイルドカード (*) である場合があります。
* *Nameexprtomaximize*: *exprto最大化*を表す結果列の省略可能な名前です。
* *NameExprToReturn*: *ExprToReturn*を表す結果列の追加の省略可能な名前です。

## <a name="returns"></a>戻り値

*Exprtomaximize*を最大化し、 *ExprToReturn*の値を返す (または行全体を返す) グループ内の行を検索し `*` ます。

## <a name="examples"></a>例

[Arg_min ()](arg-min-aggfunction.md)集計関数の例を参照してください。