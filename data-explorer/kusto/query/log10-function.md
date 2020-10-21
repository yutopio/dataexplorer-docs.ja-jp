---
title: log10 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの log10 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 978e40f3a2727b08dd404068ad364c7416361f66
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241412"
---
# <a name="log10"></a>log10()

`log10()` 常用対数関数を返します。  

## <a name="syntax"></a>構文

`log10(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数 > 0 です。

## <a name="returns"></a>戻り値

* 常用対数は10を底とする対数で、底が10の指数関数 (exp) の逆の対数です。
* `null` 引数が負の値または null の場合、または値に変換できない場合は `real` 。 

## <a name="see-also"></a>関連項目

* 自然対数 (底-e) の場合は、 [log ()](log-function.md)を参照してください。
* 底2の対数については、「 [log2 ()](log2-function.md) 」を参照してください。