---
title: round ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの round () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c281d3347e82b429ded187ee142ea13fa7594567
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345744"
---
# <a name="round"></a>round()

丸められたソースを指定した有効桁数に戻します。

## <a name="syntax"></a>構文

`round(`*ソース*[ `,` *有効桁数*]`)`

## <a name="arguments"></a>引数

* *ソース*: ラウンドが計算されるソーススカラー。
* *Precision*: 変換元が丸められる桁数。(既定値は 0)

## <a name="returns"></a>戻り値

指定した有効桁数に丸められたソース。

Round はとは異なり [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) ます。最初のは数値を特定の桁数に丸め、最後に値を指定されたビンサイズの倍数に丸めます (2.2 round (2.15, 1) は、bin (2.15, 1) は2を返します)。
 

## <a name="examples"></a>使用例

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```