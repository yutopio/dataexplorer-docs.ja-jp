---
title: 時間枠内での参加-Azure データエクスプローラー
description: この記事では、Azure データエクスプローラーの時間枠内での参加について説明します。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b1f951f23587451d62deefa5feb24e2d1fc6b612
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251115"
---
# <a name="time-window-join"></a>時間枠内での結合

多くの場合、高カーディナリティキーで2つの大規模なデータセットを結合すると便利です。操作 ID やセッション ID など、左側と右側の datetime 列間に制限を追加することにより、各左側 ($left) レコードと一致させる必要がある右側の ($right) レコードをさらに制限することができるようになりました。

関数は、次のシナリオのように、結合で役に立ちます。
* 操作 ID やセッション ID など、いくつかの高カーディナリティキーに従って、2つの大きなデータセットを結合します。
* 左側と右側の datetime 列の間に "時間間隔" の制限を追加することによって、各左側 ($left) レコードと一致させる必要がある右側の ($right) レコードを制限します。

上記の操作は、通常の Kusto join 操作とは異なります *`equi-join`* 。左辺と右辺のデータセットの間でハイカーディナリティキーを照合する部分では、システムで distance 関数を適用し、それを使用して結合を大幅に高速化することもできます。

> [!NOTE]
> Distance 関数は等値として動作しません (つまり、dist (x, y) と dist (y, z) の両方が true の場合、その dist (x, z) も true になります)。内部的には、これを "対角線結合" と呼ぶことがあります。

たとえば、比較的短い時間枠でイベントシーケンスを識別する場合は、次のスキーマを持つテーブルがあると仮定し `T` ます。

* `SessionId`: `string` 相関 id を持つ型の列。
* `EventType`: `string` レコードのイベントの種類を識別する型の列。
* `Timestamp`: 型の列は、 `datetime` レコードによって記述されたイベントが発生した日時を示します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
T
```

|SessionId|EventType|Timestamp|
|---|---|---|
|0|A|2017-10-01 00:00: 00.0000000|
|0|B|2017-10-01 00:01: 00.0000000|
|1|B|2017-10-01 00:02: 00.0000000|
|1|A|2017-10-01 00:03: 00.0000000|
|3|A|2017-10-01 00:04: 00.0000000|
|3|B|2017-10-01 00:10: 00.0000000|


**問題の説明**

クエリでは、次の質問に答える必要があります。

   イベントの種類の後にイベントの種類が含まれていたすべてのセッション Id を時間枠で検索し `A` `B` `1min` ます。

> [!NOTE]
> 上記のサンプルデータでは、このようなセッション ID のみが `0` です。

意味的には、次のクエリでは、この質問に対して効率が悪いと答えています。

```kusto
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp
    ) on SessionId
| where (End - Start) between (0min .. 1min)
| project SessionId, Start, End 

```

|SessionId|[開始]|End|
|---|---|---|
|0|2017-10-01 00:00: 00.0000000|2017-10-01 00:01: 00.0000000|

このクエリを最適化するには、時間枠が結合キーとして表されるように、以下に説明するように書き直してください。

**時間枠を考慮してクエリを書き直してください**

`datetime`値が時間枠の半分のサイズであるバケットに "分離" されるように、クエリを書き直します。 Kusto を使用して *`equi-join`* 、これらのバケット id を比較します。

```kusto
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0; // lookup bin = equal to 1/2 of the lookup window
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp,
          // TimeKey on the left side of the join is mapped to a discrete time axis for the join purpose
          TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              // TimeKey on the right side of the join - emulates event 'B' appearing several times
              // as if it was 'replicated'
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    // 'mv-expand' translates the TimeKey array range into a column
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|[開始]|End|
|---|---|---|
|0|2017-10-01 00:00: 00.0000000|2017-10-01 00:01: 00.0000000|

**実行可能なクエリ参照 (テーブルインライン)**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = datatable(SessionId:string, EventType:string, Timestamp:datetime)
[
    '0', 'A', datetime(2017-10-01 00:00:00),
    '0', 'B', datetime(2017-10-01 00:01:00),
    '1', 'B', datetime(2017-10-01 00:02:00),
    '1', 'A', datetime(2017-10-01 00:03:00),
    '3', 'A', datetime(2017-10-01 00:04:00),
    '3', 'B', datetime(2017-10-01 00:10:00),
];
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Timestamp, TimeKey = bin(Timestamp, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Timestamp,
              TimeKey = range(bin(Timestamp-lookupWindow, lookupBin),
                              bin(Timestamp, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
```

|SessionId|[開始]|End|
|---|---|---|
|0|2017-10-01 00:00: 00.0000000|2017-10-01 00:01: 00.0000000|


**50M data クエリ**

次のクエリでは、50M レコードのデータセットと ~ 1000 の Id をエミュレートし、前述の手法でクエリを実行します。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let T = range x from 1 to 50000000 step 1
| extend SessionId = rand(10000000), EventType = rand(3), Time=datetime(2017-01-01)+(x * 10ms)
| extend EventType = case(EventType <= 1, "A",
                          EventType <= 2, "B",
                          "C");
let lookupWindow = 1min;
let lookupBin = lookupWindow / 2.0;
T 
| where EventType == 'A'
| project SessionId, Start=Time, TimeKey = bin(Time, lookupBin)
| join kind=inner
    (
    T 
    | where EventType == 'B'
    | project SessionId, End=Time, 
              TimeKey = range(bin(Time-lookupWindow, lookupBin), 
                              bin(Time, lookupBin),
                              lookupBin)
    | mv-expand TimeKey to typeof(datetime)
    ) on SessionId, TimeKey 
| where (End - Start) between (0min .. lookupWindow)
| project SessionId, Start, End 
| count 
```

|Count|
|---|
|1276|
