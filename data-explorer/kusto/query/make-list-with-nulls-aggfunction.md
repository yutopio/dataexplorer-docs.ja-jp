---
title: make_list_with_nulls () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの make_list_with_nulls () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 18d10aa15e25979c0945ec26efb8bfe9a66dfde4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252211"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (集計関数)

`dynamic`Null 値を含む、グループ内の*Expr*のすべての値の (JSON) 配列を返します。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

## <a name="syntax"></a>構文

`summarize``make_list_with_nulls(` *Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 集計計算に使用される式です。

## <a name="returns"></a>戻り値

`dynamic`Null 値を含む、グループ内の*Expr*のすべての値の (JSON) 配列を返します。
演算子への入力 `summarize` が並べ替えられていない場合、結果として得られる配列内の要素の順序は定義されません。
演算子への入力が並べ替えられている場合、結果として `summarize` 得られる配列内の要素の順序によって、入力の値が追跡されます。

> [!TIP]
> 任意の [`array_sort_asc()`](./arraysortascfunction.md) [`array_sort_desc()`](./arraysortdescfunction.md) キーで順序付きリストを作成するには、関数または関数を使用します。
