---
title: round ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの round () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90d424929fe0b2034e4778ca2167e1e14dfbf79e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242904"
---
# <a name="round"></a>round()

丸められたソースを指定した有効桁数に戻します。

## <a name="syntax"></a>構文

`round(`*ソース* [ `,` *有効桁数*]`)`

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