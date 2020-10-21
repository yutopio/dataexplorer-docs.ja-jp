---
title: アークサイン ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのアークサイン () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e23ddda4bbc2e165adfd810e1dc27b5ffd14edf4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246744"
---
# <a name="asin"></a>asin()

サインが指定された数値 (の逆演算) である角度を返し [`sin()`](sinfunction.md) ます。

## <a name="syntax"></a>構文

`asin(`*閉じる*`)`

## <a name="arguments"></a>引数

* *x*: 範囲 [-1, 1] の実数。

## <a name="returns"></a>戻り値

* のアークサインの値 `x`
* `null``x`<-1 または `x` > 1 の場合