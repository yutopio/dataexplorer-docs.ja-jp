---
title: isnotnull ()-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの isnotnull () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ff472919ecda9550e7e0bcd6b403c605d029bfb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347189"
---
# <a name="isnotnull"></a>isnotnull()

`true`引数が null でない場合は、を返します。

## <a name="syntax"></a>構文

`isnotnull(`[*値*]`)`

`notnull(`[*値*] `)`-のエイリアス`isnotnull`

## <a name="example"></a>例

```kusto
T | where isnotnull(PossiblyNull) | count
```

上記の結果を得る方法はほかにもあることに注意してください。

```kusto
T | summarize count(PossiblyNull)
```