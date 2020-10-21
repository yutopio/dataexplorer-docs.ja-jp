---
title: log ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの log () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 2b7c4f0ac50c35dba0a1d59540fdaafb0cda2bfd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241456"
---
# <a name="log"></a>log()

`log()` 自然対数関数を返します。  

## <a name="syntax"></a>構文

`log(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数 > 0 です。

## <a name="returns"></a>戻り値

* 自然対数は底-e の対数 (自然指数関数 (exp) の逆) です。
* `null` 引数が負の値または null の場合、または値に変換できない場合は `real` 。 

## <a name="see-also"></a>関連項目

* 一般的な対数 (10 進) については、「 [log10 ()](log10-function.md)」を参照してください。
* 底2の対数については、「 [log2 ()](log2-function.md) 」を参照してください。