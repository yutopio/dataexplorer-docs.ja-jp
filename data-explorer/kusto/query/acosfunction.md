---
title: acos() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで acos() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: aa33714d57b319ba5a775385e8ee7d232addfe9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519331"
---
# <a name="acos"></a>acos()

コサインが指定した数値 (の逆の操作) である角度を[`cos()`](cosfunction.md)返します。

**構文**

`acos(`*X*`)`

**引数**

* *x*: 範囲 [-1, 1] の実数。

**戻り値**

* アークコサインの値`x`
* `null`< `x` -1`x`または > 1