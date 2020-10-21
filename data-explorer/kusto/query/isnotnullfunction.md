---
title: isnotnull ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isnotnull () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c198bd34161c683c6e5c6f1bde2990c0605122c8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253265"
---
# <a name="isnotnull"></a>isnotnull()

`true`引数が null でない場合は、を返します。

## <a name="syntax"></a>構文

`isnotnull(`[*値*]`)`

`notnull(`[*値*] `)` -のエイリアス `isnotnull`

## <a name="example"></a>例

```kusto
T | where isnotnull(PossiblyNull) | count
```

上記の結果を得る方法はほかにもあることに注意してください。

```kusto
T | summarize count(PossiblyNull)
```