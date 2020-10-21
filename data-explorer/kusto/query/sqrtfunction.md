---
title: sqrt ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの sqrt () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 81b31d8a692a2f68cdb8dd68b5c5f1e75057d3b0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242508"
---
# <a name="sqrt"></a>sqrt()

平方根関数を返します。  

## <a name="syntax"></a>構文

`sqrt(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数 >= 0 です。

## <a name="returns"></a>戻り値

* `sqrt(x) * sqrt(x) == x`
* 引数が負であるか、`real` 値に変換できない場合は `null`。 