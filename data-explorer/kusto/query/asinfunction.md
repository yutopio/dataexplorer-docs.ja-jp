---
title: アークサイン ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのアークサイン () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 360a936d136cd01760175cdf5b5da2718d0cfd91
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349501"
---
# <a name="asin"></a>asin()

サインが指定された数値 (の逆演算) である角度を返し [`sin()`](sinfunction.md) ます。

## <a name="syntax"></a>構文

`asin(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 範囲 [-1, 1] の実数。

## <a name="returns"></a>戻り値

* のアークサインの値`x`
* `null``x`<-1 または `x` > 1 の場合