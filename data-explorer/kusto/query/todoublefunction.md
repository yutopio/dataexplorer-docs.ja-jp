---
title: todouble ()/再生 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの todouble ()/再出力 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a1a18ffdfc28d0487464202baa759acccd3e40e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250476"
---
# <a name="todouble-toreal"></a>todouble()、toreal()

入力を型の値に変換し `real` ます。 ( `todouble()` と `toreal()` はシノニムです)。

```kusto
toreal("123.4") == 123.4
```

> [!NOTE]
> 可能であれば [、double () または real ()](./scalar-data-types/real.md) を使用することをお勧めします。

## <a name="syntax"></a>構文

`toreal(`*Expr* `)` 
 Expr `todouble(`*Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 値が型の値に変換される式 `real` 。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は型の値になり `real` ます。
変換に失敗した場合、結果は値になり `real(null)` ます。
