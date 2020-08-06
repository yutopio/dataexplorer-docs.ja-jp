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
ms.openlocfilehash: 8e93e86814adf2789d01e03173196468f085b7c2
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804102"
---
# <a name="todouble-toreal"></a>todouble()、toreal()

入力を型の値に変換し `real` ます。 ( `todouble()` と `toreal()` はシノニムです)。

```kusto
toreal("123.4") == 123.4
```

> [!NOTE]
> 可能であれば[、double () または real ()](./scalar-data-types/real.md)を使用することをお勧めします。

## <a name="syntax"></a>構文

`toreal(`*Expr* `)` 
 Expr `todouble(`*Expr*`)`

## <a name="arguments"></a>引数

* *Expr*: 値が型の値に変換される式 `real` 。

## <a name="returns"></a>戻り値

変換が成功した場合、結果は型の値になり `real` ます。
変換に失敗した場合、結果は値になり `real(null)` ます。
