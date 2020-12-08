---
title: count() (集計関数) - Azure Data Explorer | Microsoft Docs
description: この記事では、Azure Data Explorer count() (集計関数) について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/21/2020
ms.localizationpriority: high
ms.openlocfilehash: e45510b893d6e84f029764aa9fdac0d326a96f94
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513099"
---
# <a name="count-aggregation-function"></a>count() (集計関数)

要約グループあたりのレコード数を返します (集計がグループ化されていない場合は合計が返されます)。

* [summarize](summarizeoperator.md) 内での集計というコンテキストでのみ使用できます。
* [countif](countif-aggfunction.md) 集計関数を使用して、何らかの述語で `true` が返るレコードのみをカウントできます。

## <a name="syntax"></a>構文

summarize `count()`

## <a name="returns"></a>戻り値

要約グループあたりのレコード数を返します (集計がグループ化されていない場合は合計が返されます)。

## <a name="example"></a>例

頭文字が `W` である州のイベントを数える：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where State startswith "W"
| summarize Count=count() by State
```

|州|Count|
|---|---|
|ウェストバージニア|757|
|WYOMING|396|
|WASHINGTON|261|
|WISCONSIN|1850|
