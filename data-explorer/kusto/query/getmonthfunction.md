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
ms.openlocfilehash: 13254314bdbab7ddbdb74341e384867c26b6259d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347699"
---
# <a name="getmonth"></a>getmonth()

datetime から月 (1 ～ 12) を取得します。

別のエイリアス: monthoyear ()

## <a name="example"></a>例

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
