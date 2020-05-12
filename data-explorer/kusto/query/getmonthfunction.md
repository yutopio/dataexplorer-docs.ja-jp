---
title: getmonth ()-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの getmonth () について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: e04abb6eed95e5129d6878486e3781173cbf0557
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226790"
---
# <a name="getmonth"></a>getmonth()

datetime から月 (1 ～ 12) を取得します。

別のエイリアス: monthoyear ()

**例**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
