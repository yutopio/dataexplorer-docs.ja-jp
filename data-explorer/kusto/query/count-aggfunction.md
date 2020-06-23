---
title: count () (集計関数)-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーの count () (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.openlocfilehash: 6a06be43773a356e903b25b2697e75b8342ed7f8
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133475"
---
# <a name="count-aggregation-function"></a>count () (集計関数)

概要作成グループあたりのレコード数を返します (集計がグループ化されていない場合は合計で)。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。
* 一部の述語が返すレコードのみをカウントするには、 [countif](countif-aggfunction.md)集計関数を使用し `true` ます。

**構文**

まとめ`count()`

**戻り値**

概要作成グループあたりのレコード数を返します (集計がグループ化されていない場合は合計で)。

**例**

次の文字で始まる状態のイベントをカウントする `W` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|State|Count|
|---|---|
|ウェストバージニア|757|
|ワイオミング州|396|
|都|261|
|ウィスコンシン|1850|
