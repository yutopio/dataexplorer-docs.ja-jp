---
title: log2 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの log2 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: c5bab8e2f4106ddafc085fa35da424db60616a46
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243247"
---
# <a name="log2"></a>log2()

`log2()` 底2の対数関数を返します。  

## <a name="syntax"></a>構文

`log2(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 実数 > 0 です。

## <a name="returns"></a>戻り値

* 対数は底2の対数 (底2を持つ指数関数 (exp) の逆) です。
* `null` 引数が負の値または null の場合、または値に変換できない場合は `real` 。 

## <a name="see-also"></a>関連項目

* 自然対数 (底-e) の場合は、 [log ()](log-function.md)を参照してください。
* 一般的な対数 (10 進) については、「 [log10 ()](log10-function.md)」を参照してください。