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
ms.openlocfilehash: a5f5f554373331d66a08e7166249e8e24c4fbd7c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348787"
---
# <a name="count-aggregation-function"></a>count () (集計関数)

概要作成グループあたりのレコード数を返します (集計がグループ化されていない場合は合計で)。

* [集計の](summarizeoperator.md)コンテキストでのみ使用できます。
* 一部の述語が返すレコードのみをカウントするには、 [countif](countif-aggfunction.md)集計関数を使用し `true` ます。

## <a name="syntax"></a>構文

まとめ`count()`

## <a name="returns"></a>戻り値

概要作成グループあたりのレコード数を返します (集計がグループ化されていない場合は合計で)。

## <a name="example"></a>例

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
