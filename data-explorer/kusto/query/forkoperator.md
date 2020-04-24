---
title: フォークオペレータ - Azure データ エクスプローラ |マイクロソフトドキュメント
description: この記事では、Azure データ エクスプローラーのフォーク オペレータについて説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 13cd837b3874a55ec758991f5609e089daba7c75
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515200"
---
# <a name="fork-operator"></a>fork 演算子

複数のコンシューマー演算子を並列で実行します。

**構文**

*T* `|` `=``(``)` `=``(`*name*`)` *name**subquery* *subquery* [ 名前 ] サブクエリ [ 名前 ] サブクエリ .. `fork`

**引数**

* *サブクエリ*はクエリ演算子のダウンストリーム パイプラインです。
* *name*はサブクエリ結果テーブルの一時名です

**戻り値**

複数の結果表 (各サブクエリに 1 つずつ)。

**サポートされる演算子**

[`as`](asoperator.md), [`count`](countoperator.md), [`extend`](extendoperator.md), [`parse`](parseoperator.md), [`where`](whereoperator.md), [`take`](takeoperator.md), [`project`](projectoperator.md), [`project-away`](projectawayoperator.md), [`summarize`](summarizeoperator.md), [`top`](topoperator.md), [`top-nested`](topnestedoperator.md), [`sort`](sortoperator.md), [`mv-expand`](mvexpandoperator.md), [`reduce`](reduceoperator.md)

**メモ**

* [`materialize`](materializefunction.md)機能は、フォーク脚の使用[`join`](joinoperator.md)または[`union`](unionoperator.md)フォーク脚の代替として使用できます。
入力ストリームは、マテリアライズによってキャッシュされ、その後、キャッシュされた式を結合/結合脚で使用できます。

* 引数または演算子を`name`使用[`as`](asoperator.md)して指定された名前は、ツールの[`Kusto.Explorer`](../tools/kusto-explorer.md)結果タブに名前を付ける名前として使用されます。

* 単一`fork`のサブクエリでの使用は避けます。

* 演算子よりも[batch](batches.md)表形式[`materialize`](materializefunction.md)の式ステートメントを使用`fork`してバッチを使用することを好みます。

**使用例**

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