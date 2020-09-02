---
title: make_list_with_nulls () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの make_list_with_nulls () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 1d4dafdab4c727b89838f18e13b016d771262f08
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89366012"
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
> [`mv-apply`](./mv-applyoperator.md)キーによって順序付けられたリストを作成するには、演算子を使用します。 [こちら](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-make_list-aggregate-by-some-key)の例を参照してください。
