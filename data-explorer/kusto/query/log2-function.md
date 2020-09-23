---
title: log2 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの log2 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: e04d71ad0e88d42f93f9f34576c4f5f6e752ddb6
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103249"
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