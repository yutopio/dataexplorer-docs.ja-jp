---
title: フォーク演算子-Azure データエクスプローラー |Microsoft Docs
description: この記事では、Azure データエクスプローラーのフォーク演算子について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dfa4d2218c3f54a9c85644fb0ee1edf4b7c012dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247415"
---
# <a name="fork-operator"></a>fork 演算子

複数のコンシューマー操作を並行して実行します。

## <a name="syntax"></a>構文

*T* `|` `fork` [*name* `=` ] `(` *サブクエリ* `)` [*name* `=` ] `(` *サブクエリ* `)` ...

## <a name="arguments"></a>引数

* *サブ* クエリはクエリ演算子の下流のパイプラインです
* *name* は、サブクエリの結果テーブルの一時名です

## <a name="returns"></a>戻り値

複数の結果テーブル (サブクエリごとに1つ)。

**サポートされる演算子**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**ノート**

* [`materialize`](materializefunction.md) 関数 [`join`](joinoperator.md) は、分岐脚でまたはを使用するための代替として使用でき [`union`](unionoperator.md) ます。
入力ストリームは具体化によってキャッシュされた後、結合/共用脚で使用できます。

* 引数または using 演算子によって指定された名前は `name` [`as`](asoperator.md) 、ツールの [結果] タブに名前を付けるためにとして使用され [`Kusto.Explorer`](../tools/kusto-explorer.md) ます。

* 1つのサブクエリでを使用することは避けて `fork` ください。

* 演算子に対してテーブル式ステートメントの [バッチ](batches.md) を使用することをお勧め [`materialize`](materializefunction.md) `fork` します。

## <a name="examples"></a>使用例

```kusto
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 )
    ( project Timestamp, EventText | top 1000 by Timestamp desc)
    ( summarize min(Timestamp), max(Timestamp) by ActivityID )
 
// In the following examples the result tables will be named: Errors, EventsTexts and TimeRangePerActivityID
KustoLogs
| where Timestamp > ago(1h)
| fork
    ( where Level == "Error" | project EventText | limit 100 | as Errors )
    ( project Timestamp, EventText | top 1000 by Timestamp desc | as EventsTexts )
    ( summarize min(Timestamp), max(Timestamp) by ActivityID | as TimeRangePerActivityID )
    
 KustoLogs
| where Timestamp > ago(1h)
| fork
    Errors = ( where Level == "Error" | project EventText | limit 100 )
    EventsTexts = ( project Timestamp, EventText | top 1000 by Timestamp desc )
    TimeRangePerActivityID = ( summarize min(Timestamp), max(Timestamp) by ActivityID )
```