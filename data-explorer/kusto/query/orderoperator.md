---
title: order 演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの order 演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 510e2de8a30a422955c0cbfcbdf3a0f50e46dbc5
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346560"
---
# <a name="order-operator"></a>order 演算子 

入力テーブルの行を 1 つ以上の列の順序で並べ替えます。

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> Order 演算子は、sort 演算子のエイリアスです。 詳細については、「 [sort 演算子](sortoperator.md)」を参照してください。

## <a name="syntax"></a>構文

*T* `| order by` *列*[ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>引数

* *T*: 並べ替えの対象となるテーブル入力。
* *列*: 並べ替えに使用する*T*の列。 値の型は、数値、日付、時刻、または文字列にする必要があります。
* `asc` : 昇順で (小さい値から大きい値へ) 並べ替えます。 既定値は `desc`で、降順 (大きい値から小さい値へ) です。
* `nulls first`(order の既定値 `asc` ) は、先頭に null 値を挿入し `nulls last` ます。(order の既定値 `desc` ) は、null 値を末尾に配置します。

