---
title: getmonth() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの getmonth() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: b0224d8ea9c99ce72604a5b7df248394bc6317fa
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514418"
---
# <a name="getmonth"></a>getmonth()

datetime から月 (1 ～ 12) を取得します。

もう一つの別名:月数年()

**例**

```kusto
print month = getmonth(datetime(2015-10-12))
```