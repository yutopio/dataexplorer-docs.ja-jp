---
title: countif () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの countif () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5b69b50c0cf4c07934d7900937675bf6338eab9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348770"
---
# <a name="countif-aggregation-function"></a>countif () (集計関数)

*Predicate* が `true` と評価された行の数を返します。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。

「- [Count ()](count-aggfunction.md)関数」を参照してください。この関数は、述語式のない行をカウントします。

## <a name="syntax"></a>構文

`countif(`*述語*の集計`)`

## <a name="arguments"></a>引数

* *述語*: 集計計算に使用される式。 

## <a name="returns"></a>戻り値

*Predicate* が `true` と評価された行の数を返します。

> [!TIP]
> `where filter | summarize count()` の代わりの `summarize countif(filter)` の使用