---
title: acos ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの acos () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d1daf36e85eec4c8543ba14be153a9d6069e1cd1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349858"
---
# <a name="acos"></a>acos()

コサインが指定された数値 (の逆演算) である角度を返し [`cos()`](cosfunction.md) ます。

## <a name="syntax"></a>構文

`acos(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 範囲 [-1, 1] の実数。

## <a name="returns"></a>戻り値

* のアークコサインの値。`x`
* `null``x`<-1 または `x` > 1 の場合