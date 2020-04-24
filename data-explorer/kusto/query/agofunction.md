---
title: ago() - Azure データ エクスプローラー |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーでの ago() について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7d155f1a1cd113f73acc779e6c2e73d5f537e71c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519093"
---
# <a name="ago"></a>ago()

現在の UTC 時刻から指定された期間を減算します。

```kusto
ago(1h)
ago(1d)
```

`now()` と同様、この関数はステートメント内で複数回使用することができます。また、参照される UTC 時刻はすべてのインスタンスで同じになります。

**構文**

`ago(`*a_timespan*`)`

**引数**

* *a_timespan*: 現在の UTC 時刻 (`now()`) から減算する期間。

**戻り値**

`now() - a_timespan`

**例**

過去 1 時間以内のタイムスタンプを持つすべての行は、次のようになります。

```kusto
T | where Timestamp > ago(1h)
```