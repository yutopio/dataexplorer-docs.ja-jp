---
title: totimespan ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの totimespan () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0779a6260cc87f8a602f4751d28c33de9bacb57a
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243752"
---
# <a name="totimespan"></a>totimespan()

入力を [timespan](./scalar-data-types/timespan.md) スカラーに変換します。

```kusto
totimespan("0.00:01:00") == time(1min)
```

## <a name="syntax"></a>構文

`totimespan(Expr)`

## <a name="arguments"></a>引数

* *`Expr`*: [Timespan](./scalar-data-types/timespan.md)に変換される式。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は [timespan](./scalar-data-types/timespan.md) 値になります。
それ以外の場合、結果は null になります。
