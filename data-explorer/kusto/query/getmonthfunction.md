---
title: getmonth ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの getmonth () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: 1b3f87c7be8f4b2e12fc0136c163c4df0766eed8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252982"
---
# <a name="getmonth"></a>getmonth()

datetime から月 (1 ～ 12) を取得します。

別のエイリアス: monthoyear ()

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
