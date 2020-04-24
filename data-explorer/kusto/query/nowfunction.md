---
title: now() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーで now() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c1a130cfbd45c35ff1ba26ed6c47986fc186c89c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512055"
---
# <a name="now"></a>now()

現在の UTC 時刻を返します。
この関数はステートメント内で複数回使用することができます。また、参照される時刻はすべてのインスタンスで同じになります。

```kusto
now()
now(-2d)
```

**構文**

`now(`[*オフセット*]`)`

**引数**

* *offset*: `timespan`A 、現在の UTC クロック時刻に追加されます。 既定値は0。

**戻り値**

`datetime`型の現在の UTC 時刻。

`now()` + *オフセット* 

**例**

述語によって特定されるイベント以降の期間を判断します。

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```