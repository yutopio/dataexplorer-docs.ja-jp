---
title: log ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの log () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: e81266bf43da93d2b36f0be5846e5d74f3157c7f
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103212"
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