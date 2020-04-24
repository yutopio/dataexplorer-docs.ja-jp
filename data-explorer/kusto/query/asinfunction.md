---
title: asin() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで asin() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5db112daeba59dd841b02df8ba1a41185654ad6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518549"
---
# <a name="asin"></a>asin()

指定した数値 (の逆の操作) が正弦の角度を[`sin()`](sinfunction.md)返します。

**構文**

`asin(`*X*`)`

**引数**

* *x*: 範囲 [-1, 1] の実数。

**戻り値**

* アーク正弦の値`x`
* `null`< `x` -1`x`または > 1