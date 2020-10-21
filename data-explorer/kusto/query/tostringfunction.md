---
title: tostring ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの tostring () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 43797dcaa5e1a18b6a84f15fc0af89d88d814ff6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243798"
---
# <a name="tostring"></a>tostring()

入力を文字列形式に変換します。

```kusto
tostring(123) == "123"
```

## <a name="syntax"></a>構文

`tostring(`*`Expr`*`)`

## <a name="arguments"></a>引数

* *`Expr`*: 文字列に変換される式。 

## <a name="returns"></a>戻り値

*`Expr`* 値が null 以外の場合、結果はの文字列表現になり *`Expr`* ます。
*`Expr`* 値が null の場合、結果は空の文字列になります。
 