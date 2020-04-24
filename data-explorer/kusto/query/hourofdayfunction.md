---
title: アワーオフデイ() - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの 1 日の時間( 1 時間)について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 08fdb45af5eec7f71d491725ea58a7d72c06371b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514078"
---
# <a name="hourofday"></a>hourofday()

指定された日付の時間数を表す整数を返します。

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

**構文**

`hourofday(`*a_date*`)`

**引数**

* `a_date`: `datetime`。

**戻り値**

`hour number`その日の(0-23)。