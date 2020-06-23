---
title: todouble ()/再生 ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーでの todouble ()/再出力 () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e62432773d99d74a46022cad3199f3bab0cae50b
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128465"
---
# <a name="todouble-toreal"></a>todouble ()、は、再生 ()

入力を型の値に変換し `real` ます。 ( `todouble()` と `toreal()` はシノニムです)。

```kusto
toreal("123.4") == 123.4
```

**構文**

`toreal(`*Expr* `)` 
 Expr `todouble(`*Expr*`)`

**引数**

* *Expr*: 値が型の値に変換される式 `real` 。

**戻り値**

変換が成功した場合、結果は型の値になり `real` ます。
変換に失敗した場合、結果は値になり `real(null)` ます。

*注*: 可能であれば[、double () または real ()](./scalar-data-types/real.md)を使用することをお勧めします。